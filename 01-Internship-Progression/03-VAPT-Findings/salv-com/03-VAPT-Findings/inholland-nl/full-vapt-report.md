# Vulnerability Assessment & Penetration Testing Report
## inholland.nl

---

## About inholland.nl

inholland.nl is an official website of Inholland University of Applied Sciences of Netherlands. This website is a secure university website which follows EU GDPR rules. Inholland protects students and staff data by using IT security measures like secure login and monitoring. This specific report is presented to give more security assurance to inholland which will help them to make their website security more advanced and with minimal or zero flow expect.

---

## Confidentiality

This report and all associated findings are strictly confidential and intended solely for Inholland University of Applied Sciences. The contents of this report shall not be disclosed, shared, distributed, or reproduced in whole or in part to any third party without the prior written permission of the Inholland Security Team.

---

## Table of Contents

1. PROJECT SUMMARY
2. VULNERABILITY CHART
3. TECHNICAL FINDINGS (Reports)
4. APPENDIX A | Risk Definitions
5. APPENDIX B | Tools Usage
6. DISCLAIMER

---

## Project Summary

Rudra Tech Security performed a security assessment of the inholland.nl web application to identify potential vulnerabilities, evaluate the level of risk they pose to the organization, and provide actionable recommendations to reduce these risks. This report has been prepared by Rudra Tech Security to provide the Inholland Security Team with detailed information on each vulnerability identified during the web application penetration testing, including potential business impact and specific remediation guidance.

---

## Vulnerability Chart

| VULNERABILITY NAME | SEVERITY |
|--------------------|----------|
| CWE-248 (Unhandled Exception) | Low (P4) |
| Hyperlink or Clickjacking | Low (P4) |
| CWE-20 Improper Input Validation | Low (P4) |
| Origin IP Disclose | Informational (P5) |
| No Rate Limit | Low (P4) |

---

## Technical Finding 1

### Unhandled Exception

**Description**
The application suffers from improper input validation and unhandled exceptions in backend request processing, aligning with CWE-20 (Improper Input Validation) and CWE-248 (Unhandled Exception). User-controlled input reaches backend logic without sufficient validation, leading to model binding or parsing errors. These exceptions are not gracefully handled, causing the server to crash or return internal error responses instead of proper client error messages such as HTTP 400.

**Affected URL**
`https://studiekeuzetest.inholland.nl/Deel1/Vraag`

**Bug Type**
Input Validation / Error Handling

**CWE**
CWE-20, CWE-248

**Severity**
Low (P4)

**Impact**
This issue does not directly enable exploitation or unauthorized access; however, it weakens overall application robustness and security posture. Unhandled backend exceptions can leak internal behavior, assist attackers in understanding backend logic, and increase the risk of denial-of-service conditions. While limited in standalone impact, such weaknesses may be leveraged in conjunction with other vulnerabilities and are therefore classified as Low severity (P4).

**Recommendation**
Implement strict server-side input validation for all user-controlled parameters and ensure robust exception handling within backend logic. Model binding and parsing errors should be caught and handled gracefully, returning standardized client error responses (e.g., HTTP 400 Bad Request) without exposing internal details. Additionally, centralized error handling and proper logging should be enforced to improve application stability and security hygiene.

**Steps to Reproduce**
1. Capture the `https://studiekeuzetest.inholland.nl/Deel1/Vraag` URL request in Burp Suite and send it to the Repeater.
2. Observe the response.
3. Change the characters of numbers in request with HTML code and observe the response.

---

## Technical Finding 2

### Hyperlink | Clickjacking

**Description**
An attacker can add their name to a URL in order to send email containing malicious hyperlinks on email input of the website page where personality test is given. Before starting the test there is requirement of adding name and email, and after completing the test the site will send the result on your email. By adding `evil.com` instead of name, the website is not handling the inputs and also not sanitizing.

**Affected URL**
[Not specified in document - refer to steps]

**Bug Type**
Improper Input Validation [hyperlink]

**Severity**
Low (P5)

**Impact**
This permits users to send malicious/phishing links to potential clients. It could also have an effect on how spam filters treat emails. In addition, the presence of malicious or spam-like content originating from the platform may negatively affect the email and domain reputation associated with `studiekeuzetest.inholland.nl`, including `https://studiekeuzetest.inholland.nl/Deel1/UitslagAlgemeen`. This can lead to emails being flagged or blocked by spam filters, reducing deliverability of legitimate communications and potentially impacting user trust, brand reputation, and business operations.

**Recommendation**
- Validation of allowed characters, formats, and length for all input fields
- Blocking or sanitizing URLs, scripts, and suspicious patterns commonly used in phishing or XSS attacks
- Enforcing email verification to prevent automated or fraudulent account creation
- Applying rate limiting and CAPTCHA mechanisms to reduce automated abuse
- Logging and monitoring sign-up activity to detect and respond to suspicious behavior

**Steps to Reproduce**
1. Navigate to the page where you have to enter name and email before starting the test.
2. Add `evil.com` in the name field and a valid email of the target.
3. After completing the test, when the victim checks their mail, there is a malicious link visible.
4. The link redirects the victim to a given website where the attacker can add their own website URL and potentially capture personal credentials.

**Proof of Concept**
Video POC attached: `report 2 hyperlink.mp4`

---

## Technical Finding 3

### Improper Input Validation

**Description**
The application fails to properly validate and sanitize user-supplied input in form fields. As a result, arbitrary and potentially malicious content (such as phishing URLs) can be submitted and accepted without restriction. This untrusted input is processed as legitimate data and may later be displayed to users or included in outbound communications, enabling abuse of the platform for malicious purposes.

**Affected URL**
[Not specified - interview form functionality]

**Affected Functionality**
User input handling in form submission on interview form

**Bug Type**
Improper Input Validation | OWASP Top 10: A03 - Injection

**Severity**
Medium to High

**Impact**
- Inject phishing or malicious hyperlinks into application forms
- Distribute harmful links to users or potential clients while impersonating a trusted source
- Perform social engineering attacks, potentially leading to credential theft or malware installation
- Damage the reputation of the application's domain and email infrastructure
- Increase the likelihood of legitimate emails being flagged as spam by email providers
- Risk domain or IP blacklisting, reducing email deliverability and user trust

**Recommendation**
- Implement strict server-side input validation for all user-submitted fields
- Use allow-lists for permitted characters, formats, and content types
- Detect and block URLs, scripts, and suspicious patterns where not explicitly required
- Sanitize all user input before storage and output
- Require email verification before allowing form submissions to impact other users

**Steps to Reproduce**
1. Navigate to the interview form page where the limit for all input is 255 characters.
2. In the number input sections, add "000000000000" multiple times to test if backend verification exists.
3. After completing the form, observe that the website accepts all inputs without validation.
4. The page confirms with "Thank you for your request!" message.

---

## Technical Finding 4

### Origin IP Disclosure

**Description**
The application exposes the origin IP address of a self-managed GitLab instance directly on the login and registration pages. Instead of being protected behind a reverse proxy, WAF, or CDN, the GitLab service is accessible via its raw backend IP address.

**Affected URL**
`https://appliedmath.toi.inholland.nl/users/sign_in`

**Bug Type**
Information Disclosure

**Severity**
Medium

**Impact**
Impact is medium due to the sensitivity of the exposed service and its role. Attackers can identify and directly target the GitLab origin server, bypassing security layers such as WAFs, load balancers, or reverse proxies. This allows for targeted attacks—including brute force, credential stuffing, and enumeration—against GitLab authentication endpoints.

**Recommendation**
To mitigate this issue, the following actions are strongly recommended:
- Place the GitLab instance behind a reverse proxy or load balancer
- Restrict direct IP-based access using firewall rules
- Enforce access only through approved domain names

**Steps to Reproduce**
1. After subdomain enumeration, found `https://appliedmath.toi.inholland.nl/users/sign_in` which directly redirects to the login page.
2. Started reconnaissance through Shodan for the origin IP of the same subdomain.
3. Discovered the origin IP: `https://13.94.69.36/users/sign_in`

---

## Technical Finding 5

### Missing Rate Limiting on Login Page

**Description**
The GitLab login page does not implement effective rate limiting or brute-force protection mechanisms. Authentication requests can be sent repeatedly without restriction, throttling, or temporary account lockout. This issue exists on both: the public-facing subdomain and the directly accessible origin IP of the GitLab instance.

**Affected URL**
`https://appliedmath.toi.inholland.nl/users/sign_in`

**Bug Type**
Missing Rate Limiting on Login Page

**Severity**
HIGH

**Impact**
Impact is HIGH, especially due to the exposure of GitLab authentication. Attackers can conduct brute-force password attacks against GitLab user accounts. They can bypass perimeter security by attacking the origin IP directly, avoiding WAF or CDN protections. Because GitLab commonly stores sensitive intellectual property and deployment secrets, successful exploitation could lead to critical organizational impact.

**Recommendation**
To remediate this vulnerability, it is strongly recommended to:
- Implement rate limiting on all authentication endpoints
- Enforce account lockout or progressive delays after multiple failed login attempts
- Enable CAPTCHA after a defined number of failed attempts
- Apply protections consistently on all access points

**Steps to Reproduce**
1. Capture the login page request in Burp Suite.
2. Send it to Intruder and select the position on the password field.
3. Test if the website accepts multiple password attempts on the same email.
4. Observe that no rate limiting or account lockout occurs.

---

## Conclusion

These issues are fixable configuration gaps, and once input validation, IP protection, and rate limiting are properly implemented, the platform will be significantly more secure, reliable, and resistant to attacks.

---

**Report Prepared By:**
Mayuri S. Patwardhan
Cybersecurity Analyst
21|01|2026
