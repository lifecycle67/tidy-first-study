```java
protected void doFilterInternal(HttpServletRequest req, HttpServletResponse res, FilterChain filterChain)
					throws ServletException, IOException {
		String requestPath = req.getRequestURI();
		if (isPublic(req.getContextPath(), requestPath)) {
			filterChain.doFilter(req, res);
			return; // 로그인에서는 Authorization 헤더 무시
		}

		// (7) 선언과 초기화를 함께 옮기기: 변수가 의미를 얻는 순간에 선언과 초기화를 함께 둔다.
		String authorization = req.getHeader("Authorization");
		if(authorization == null || !authorization.startsWith("Bearer ")) {
			writeJsonErrorResponse(req, res, HttpServletResponse.SC_UNAUTHORIZED, "Invalid token");
			return;
		}

		String token = authorization.split(" ")[1];
		if(token == null) return; // (1) 보호 구문: 실패 케이스를 상위로 옮긴다.

		try{
			JwtProps jwtProps = applicationConfig.getJwt(); // (7) 선언과 초기화를 함께 옮기기: 변수가 의미를 얻는 순간에 선언과 초기화를 함께 둔다.
			String iss = jwtProvider.getIsuuer(token);

			if (!jwtProps.getIssuer().equals(iss)) {
				writeJsonErrorResponse(req, res, HttpServletResponse.SC_UNAUTHORIZED, "Invalid issuer");
				return;
			}

			var audiences = jwtProvider.getAudiences(token); // List<String> 반환 가정
			if (audiences == null || !audiences.contains(jwtProps.getAudience())) {
				writeJsonErrorResponse(req, res, HttpServletResponse.SC_UNAUTHORIZED, "Invalid audience");
				return;
			}

			//임시 access token의 경우 public path and MFA path 이외에는 접근 불가하도록 처리
			// (5) 사람이 코드를 읽는 순서대로 코드를 재배치
			JwtTokenType tokenType = JwtTokenType.valueOf(jwtProvider.getType(token)); // (7) 선언과 초기화를 함께 옮기기: 변수가 의미를 얻는 순간에 선언과 초기화를 함께 둔다.
			if(tokenType == JwtTokenType.TEMPORARY && !isMfa(req.getContextPath(), requestPath)) {
				writeJsonErrorResponse(req, res, HttpServletResponse.SC_FORBIDDEN, "cannot access protected resources");
				return;
			}

			CustomUserDetails userDetails = buildUserDetailsFromToken(token);
			setSecurityContext(userDetails);

			filterChain.doFilter(req, res);
		} catch(AuthException.InvalidJwtTokenException e) {
			// JWT가 유효하지 않거나 만료된 경우 처리
			writeJsonErrorResponse(req, res, HttpServletResponse.SC_UNAUTHORIZED, "Invalid token");
		} catch (AuthException.ExpiredAuthenticationException e) {
			var errorCode = AcmeErrorCode.ACME_206.toString();
			writeJsonErrorResponse(req, res, HttpServletResponse.SC_UNAUTHORIZED, "Expired token", errorCode);
		}
	}

	private CustomUserDetails buildUserDetailsFromToken(String token) {
		String workspace = jwtProvider.getWorkspace(token);
		String custNo = dbConfig.getCustMap().get(workspace);
		String userId = jwtProvider.getUserId(token);
		String authCode = jwtProvider.getAuthCode(token);
		String jwi =  jwtProvider.getJwi(token);
		String sessionId = jwtProvider.getSessionId(token);
		boolean keepLoggedIn = jwtProvider.getIsKeepLoggedIn(token);
		TimeZone timeZone = jwtProvider.getTimeZone(token);
		Locale locale = Optional.ofNullable(jwtProvider.getLocale(token))
						.map(Locale::new)
						.orElse(null);

		CustomUserDetails customUserDetails = CustomUserDetails.builder()
						.custNo(custNo)
						.workspace(workspace)
						.userId(userId)
						.authCode(AuthCode.fromString(authCode))
						.jwi(jwi)
						.sessionId(sessionId)
						.isKeepLoggedIn(keepLoggedIn)
						.timeZone(timeZone)
						.locale(locale)
						.build();
		return customUserDetails;
	}

	private void setSecurityContext(CustomUserDetails userDetails) {
		Authentication authToken = new UsernamePasswordAuthenticationToken(
						userDetails,
						null,
						userDetails.getAuthorities()
		);
		SecurityContextHolder.getContext().setAuthentication(authToken);
	}