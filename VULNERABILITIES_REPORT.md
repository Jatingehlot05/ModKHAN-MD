# KHAN-MD Security Vulnerabilities Report

**Report Date:** 2025-12-29  
**Audited Packages:** 911  
**Total Vulnerabilities Found:** 12

---

## Executive Summary

A security audit of the KHAN-MD dependencies revealed **12 vulnerabilities** across various severity levels:

| Severity | Count |
|----------|-------|
| 🔴 Critical | 2 |
| 🟠 High | 5 |
| 🟡 Moderate | 4 |
| 🟢 Low | 1 |

**Immediate Action Required:** Critical and High severity vulnerabilities should be addressed before production deployment.

---

## Critical Vulnerabilities (2)

### 1. Request - Server-Side Request Forgery (SSRF)
**Package:** `request@2.88.2`  
**Severity:** Critical  
**CVE:** GHSA-p8p7-x288-28g6  
**CVSS Score:** 6.1  
**CWE:** CWE-918 (Server-Side Request Forgery)

**Description:**
The `request` package is deprecated and contains a Server-Side Request Forgery vulnerability that allows attackers to make unauthorized requests from the server.

**Impact:**
- Attackers could make the bot send requests to internal network resources
- Potential data exfiltration
- Access to restricted internal services

**Affected:** `request@<=2.88.2`  
**Fix Available:** ❌ No (package is deprecated)

**Recommendation:**
```bash
# Replace 'request' with 'axios' (already in dependencies)
# Update code to use axios instead of request
npm uninstall request
# Then update gtts package or find alternative
```

**Manual Fix Required:** The `gtts` package depends on `request`. Consider:
1. Finding an alternative to `gtts` package
2. Forking and updating `gtts` to use `axios`
3. Accepting the risk and implementing strict input validation

---

### 2. Form-Data - Insecure Random Boundary Generation
**Package:** `form-data@<2.5.4` (indirect via request)  
**Severity:** Critical  
**CVE:** GHSA-fjxv-7rqg-78g4  
**CWE:** CWE-330 (Use of Insufficiently Random Values)

**Description:**
The `form-data` package uses an unsafe random function for choosing multipart form boundaries, which could be predicted by attackers.

**Impact:**
- Predictable form boundaries
- Potential for boundary injection attacks
- Data manipulation in multipart uploads

**Affected:** `form-data@<2.5.4`  
**Fix Available:** ❌ No (indirect dependency via deprecated `request`)

**Recommendation:**
Same as above - remove `request` package dependency.

---

## High Severity Vulnerabilities (5)

### 3. Axios - Cross-Site Request Forgery (CSRF)
**Package:** `axios@<=0.30.1` (indirect dependencies)  
**Severity:** High  
**CVE:** GHSA-wf5p-g6vw-rhxx  
**CVSS Score:** 8.8  
**CWE:** CWE-352 (CSRF)

**Description:**
Multiple packages use outdated versions of axios that are vulnerable to CSRF attacks.

**Affected Packages:**
- `google-tts-api@>=2.0.0`
- `translate-google-api@*`
- `wa-sticker-formatter@>=2.0.0`

**Impact:**
- Unauthorized actions performed on behalf of users
- Token theft
- Session hijacking

**Fix Available:** ✅ Partial

**Recommendations:**
```bash
# Downgrade google-tts-api
npm install google-tts-api@0.0.6

# Downgrade wa-sticker-formatter
npm install wa-sticker-formatter@1.6.0

# For translate-google-api: No fix available
# Consider alternative: @vitalets/google-translate-api (already installed)
```

---

### 4. Sharp - libwebp Vulnerability (CVE-2023-4863)
**Package:** `sharp@<0.32.6` (indirect via wa-sticker-formatter)  
**Severity:** High  
**CVE:** GHSA-54xq-cgqr-rpm3, CVE-2023-4863  
**CVSS Score:** 7.8

**Description:**
The `sharp` package contains a vulnerable version of the libwebp library that could lead to remote code execution when processing malicious WebP images.

**Impact:**
- Remote code execution
- Memory corruption
- Denial of service
- Complete system compromise

**Fix Available:** ✅ Yes (via wa-sticker-formatter downgrade)

**Recommendation:**
```bash
# Downgrade to safe version
npm install wa-sticker-formatter@1.6.0
```

---

## Moderate Severity Vulnerabilities (4)

### 5. Tough-Cookie - Prototype Pollution
**Package:** `tough-cookie@<4.1.3` (indirect via request)  
**Severity:** Moderate  
**CVE:** GHSA-72xf-g2v4-qvf3  
**CVSS Score:** 6.5  
**CWE:** CWE-1321 (Improperly Controlled Modification)

**Description:**
Prototype pollution vulnerability that could allow attackers to modify object prototypes.

**Impact:**
- Property injection
- Application behavior modification
- Potential for privilege escalation

**Fix Available:** ❌ No (indirect dependency via deprecated `request`)

---

### 6. Yargs-Parser - Prototype Pollution
**Package:** `yargs-parser@<=5.0.0` (indirect via gtts)  
**Severity:** Moderate  
**CVE:** GHSA-p9pc-299p-vxgp  
**CVSS Score:** 5.3  
**CWE:** CWE-915, CWE-1321

**Description:**
Prototype pollution in yargs-parser allows attackers to add or modify properties of Object.prototype.

**Impact:**
- Application logic bypass
- Denial of service
- Potential remote code execution

**Fix Available:** ❌ No (indirect dependency via gtts)

---

### 7. Yargs - Prototype Pollution (via yargs-parser)
**Package:** `yargs@4.0.0-alpha1 - 7.0.0-alpha.3 || 7.1.1`  
**Severity:** Moderate  
**Via:** yargs-parser vulnerability

**Fix Available:** ❌ No (indirect dependency via gtts)

---

### 8. GTTS - Multiple Vulnerabilities
**Package:** `gtts@*`  
**Severity:** Moderate  
**Via:** request, yargs vulnerabilities

**Description:**
The gtts package is affected by vulnerabilities in its dependencies (request and yargs).

**Fix Available:** ❌ No

**Recommendation:**
Consider alternative TTS packages:
```bash
# Alternative 1: Use google-tts-api (already installed)
# Alternative 2: Use external TTS API services
# Alternative 3: Fork and update gtts dependencies
```

---

## Low Severity Vulnerabilities (1)

### 9. Cookie - Out of Bounds Characters
**Package:** `cookie@<0.7.0`  
**Severity:** Low  
**CVE:** GHSA-pxg6-pf52-xh8x  
**CWE:** CWE-74 (Improper Neutralization)

**Description:**
The cookie package accepts cookie name, path, and domain with out of bounds characters.

**Impact:**
- Potential cookie injection
- HTTP response splitting (limited)

**Fix Available:** ✅ Yes

**Recommendation:**
```bash
npm install cookie@^1.1.1
```

---

## Remediation Plan

### Immediate Actions (Before Production)

1. **Update Cookie Package**
   ```bash
   npm install cookie@^1.1.1
   ```

2. **Downgrade Vulnerable Packages**
   ```bash
   npm install google-tts-api@0.0.6
   npm install wa-sticker-formatter@1.6.0
   ```

3. **Remove or Replace Request Package**
   ```bash
   npm uninstall request
   # Update code to use axios for HTTP requests
   ```

4. **Find Alternative for GTTS**
   - Option A: Use `google-tts-api` (already installed)
   - Option B: Use external TTS service
   - Option C: Fork and update gtts

5. **Remove or Replace translate-google-api**
   - Use `@vitalets/google-translate-api` instead (already installed)
   ```bash
   npm uninstall translate-google-api
   ```

### Updated package.json (Recommended Changes)

```json
{
  "dependencies": {
    "cookie": "^1.1.1",
    "google-tts-api": "0.0.6",
    "wa-sticker-formatter": "^4.4.4"
  }
}
```

**Remove these vulnerable packages:**
- ❌ `request` (deprecated, critical vulnerabilities)
- ❌ `gtts` (depends on vulnerable request)
- ❌ `translate-google-api` (vulnerable axios version)

---

## Security Testing Commands

### Check Current Vulnerabilities
```bash
npm audit
```

### Attempt Automatic Fixes
```bash
npm audit fix
```

### Force Update (May Break Compatibility)
```bash
npm audit fix --force
```

### View Detailed Report
```bash
npm audit --json
```

---

## Additional Security Measures

### 1. Input Validation
Implement strict input validation for all user inputs:
```javascript
const sanitize = (input) => {
  // Remove dangerous characters
  return input.replace(/[<>\"\'&]/g, '');
};
```

### 2. Rate Limiting
Prevent abuse by implementing rate limiting:
```javascript
const rateLimit = require('express-rate-limit');
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100
});
app.use(limiter);
```

### 3. Regular Updates
Set up automated dependency updates:
```bash
# Using npm-check-updates
npm install -g npm-check-updates
ncu -u
npm install
```

### 4. Security Headers
Add security headers to Express:
```javascript
const helmet = require('helmet');
app.use(helmet());
```

---

## Monitoring & Alerts

### Set Up Dependabot (GitHub)
1. Go to repository settings
2. Enable Dependabot alerts
3. Enable automatic security updates

### Regular Audits
Schedule regular security audits:
- **Daily:** Check for new CVEs
- **Weekly:** Run `npm audit`
- **Monthly:** Full security review

---

## False Positives & Acceptable Risks

Some vulnerabilities may be acceptable depending on usage:

| Package | Vulnerability | Acceptable If... |
|---------|---------------|------------------|
| request | SSRF | Not processing user-provided URLs |
| cookie | Out of bounds | Not using custom cookie values |

**Document accepted risks** and implement compensating controls.

---

## Compliance Notes

### OWASP Top 10 Considerations
- ✅ A1: Injection - Validate all inputs
- ✅ A2: Broken Auth - Secure SESSION_ID
- ✅ A3: Sensitive Data - Encrypt at rest
- ✅ A4: XML External Entities - Not applicable
- ✅ A5: Broken Access Control - Validate permissions
- ✅ A6: Security Misconfiguration - Review configs
- ✅ A7: XSS - Sanitize outputs
- ✅ A8: Insecure Deserialization - Validate data
- ✅ A9: Known Vulnerabilities - THIS REPORT
- ✅ A10: Insufficient Logging - Implement logging

---

## References

- [npm audit documentation](https://docs.npmjs.com/cli/v8/commands/npm-audit)
- [GitHub Advisory Database](https://github.com/advisories)
- [NIST National Vulnerability Database](https://nvd.nist.gov/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)

---

## Conclusion

**Current Security Posture:** ⚠️ NEEDS ATTENTION

The KHAN-MD bot has several critical and high severity vulnerabilities that should be addressed before production deployment. Priority should be given to:

1. Replacing deprecated `request` package
2. Updating axios versions in dependencies
3. Implementing input validation and sanitization
4. Regular security monitoring

**Estimated Time to Remediate:** 2-4 hours for manual package replacement and testing.

---

*Report Generated: 2025-12-29*  
*Next Review: 2026-01-12 (2 weeks)*
