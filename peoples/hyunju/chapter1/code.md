```java
protected void doFilterInternal(HttpServletRequest req, HttpServletResponse res, FilterChain filterChain)
			throws ServletException, IOException {
		JwtProps jwtProps = applicationConfig.getJwt();
		String authorization = req.getHeader("Authorization");

		String path = req.getRequestURI();
		if (isPublic(req.getContextPath(),path)) {
			filterChain.doFilter(req, res);
			return; // 로그인에서는 Authorization 헤더 무시
		}

		if(authorization == null || !authorization.startsWith("Bearer ")) {
			sendJson(req, res, HttpServletResponse.SC_UNAUTHORIZED, "Invalid token");
			return;
		}

		String token = authorization.split(" ")[1];

		try{
			JwtTokenType tokenType = JwtTokenType.valueOf(jwtProvider.getType(token));

			String iss = jwtProvider.getIsuuer(token);
			if (!jwtProps.getIssuer().equals(iss)) {
				sendJson(req, res, HttpServletResponse.SC_UNAUTHORIZED, "Invalid issuer");
				return;
			}

			var audiences = jwtProvider.getAudiences(token); // List<String> 반환 가정
			if (audiences == null || !audiences.contains(jwtProps.getAudience())) {
				sendJson(req, res, HttpServletResponse.SC_UNAUTHORIZED, "Invalid audience");
				return;
			}

			//내부 세션 생성
			String workspace = jwtProvider.getWorkspace(token);
			String custNo = dbConfig.getCustMap().get(workspace);
			String userId = jwtProvider.getUserId(token);
			String authCode = jwtProvider.getAuthCode(token);
			String jwi =  jwtProvider.getJwi(token);
			String sessinId = jwtProvider.getSessionId(token);
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
																					.sessionId(sessinId)
																					.isKeepLoggedIn(keepLoggedIn)
																					.timeZone(timeZone)
																					.locale(locale)
																					.build();

			Authentication authToken = new UsernamePasswordAuthenticationToken(customUserDetails, null, customUserDetails.getAuthorities());
			SecurityContextHolder.getContext().setAuthentication(authToken);

			//임시 access token의 경우 public path and MFA path 이외에는 접근 불가하도록 처리
			if(tokenType == JwtTokenType.TEMPORARY && !isMfa(req.getContextPath(), path)) {
				sendJson(req, res, HttpServletResponse.SC_FORBIDDEN, "cannot access protected resources");
				return;
			}

			filterChain.doFilter(req, res);
		} catch(AuthException.InvalidJwtTokenException e) {
			// JWT가 유효하지 않거나 만료된 경우 처리
			sendJson(req, res, HttpServletResponse.SC_UNAUTHORIZED, "Invalid token");
		} catch (AuthException.ExpiredAuthenticationException e) {
      var errorCode = AcmeErrorCode.ACME_206.toString();
      sendJson(req, res, HttpServletResponse.SC_UNAUTHORIZED, "Expired token", errorCode);
    }
	}