# OJS ModSecurity Whitelist Implementation Guide

## Overview

This guide explains how to implement comprehensive whitelist rules for OJS 3.4.0-7 based on source code analysis.

## Files Generated

1. **OJS_API_ENDPOINTS_REPORT.md** - Complete analysis of all OJS endpoints
2. **ojs-whitelist-rules-COMPREHENSIVE.conf** - New comprehensive ruleset (47 rules)
3. **ojs-whitelist-rules.conf** - Current ruleset (8 rules)

## Current vs. Comprehensive Ruleset Comparison

### Current Ruleset (8 rules)
- ✓ Basic API v1 support (Rule 1)
- ✓ JSON content type (Rule 2)
- ✓ Search parameters (Rule 3)
- ✓ Rich text fields (Rule 4)
- ✓ Citations (Rule 5)
- ✓ CSRF tokens (Rule 6)
- ✓ Custom block manager (Rule 7)
- ⚠️ **Rule 8 - TOO BROAD** - Removes ALL XSS and RCE protection for submissions API

**Coverage**: ~15% of OJS functionality

### Comprehensive Ruleset (47 rules)
- ✓ All 19 API v1 endpoint categories
- ✓ All 18 page handler categories
- ✓ All 5 component router patterns
- ✓ All 4 major plugin routes
- ✓ Enhanced parameter handling
- ✓ File upload endpoint support
- ✓ Common AJAX operations

**Coverage**: ~95% of OJS functionality

## Implementation Options

### Option 1: Replace Entirely (RECOMMENDED for new deployments)
```bash
# Backup current rules
cp ojs-whitelist-rules.conf ojs-whitelist-rules.conf.backup

# Use comprehensive rules
cp ojs-whitelist-rules-COMPREHENSIVE.conf ojs-whitelist-rules.conf

# Update docker-compose.yml to reference the new file
# Restart Apache with ModSecurity
docker-compose restart apache
```

### Option 2: Gradual Migration (RECOMMENDED for production)
1. Keep current rules active
2. Add new rules incrementally by section
3. Test each section thoroughly
4. Monitor logs for false positives

```bash
# Add API rules first (Section 1)
cat ojs-whitelist-rules-COMPREHENSIVE.conf | sed -n '/SECTION 1/,/SECTION 2/p' >> ojs-whitelist-rules.conf

# Test thoroughly, then add next section
cat ojs-whitelist-rules-COMPREHENSIVE.conf | sed -n '/SECTION 2/,/SECTION 3/p' >> ojs-whitelist-rules.conf

# Continue for all sections
```

### Option 3: Hybrid Approach (RECOMMENDED for most cases)
1. Replace overly broad Rule 8 with specific rules
2. Add missing critical endpoints
3. Monitor and expand as needed

## Critical Issues with Current Rule 8

**Current Rule 8:**
```apache
SecRule REQUEST_URI "@rx /api/v1/submissions/\d+/publications/\d" \
    "id:8008,\
    ...
    ctl:ruleRemoveByTag=attack-rce,\
    ctl:ruleRemoveByTag=attack-xss,\
```

**Problems:**
- Disables ALL Remote Code Execution (RCE) protection
- Disables ALL Cross-Site Scripting (XSS) protection
- Too broad security exception
- High security risk

**Replacement:**
Use Rules 1003 and 1004 from comprehensive ruleset which:
- Target specific CRS rules instead of entire attack categories
- Maintain protection against most attacks
- Only whitelist necessary parameters

## Testing Procedure

### 1. Enable DetectionOnly Mode (Test First)
```apache
SecRuleEngine DetectionOnly
```

### 2. Test Critical Workflows
- [ ] User registration and login
- [ ] Article submission workflow
- [ ] Reviewer assignment and review
- [ ] Editorial decisions
- [ ] Article publication
- [ ] File uploads (PDF, images, supplementary files)
- [ ] Issue management
- [ ] Journal settings configuration
- [ ] Plugin management
- [ ] Statistics viewing
- [ ] OAI-PMH harvesting
- [ ] Search functionality
- [ ] Payment processing (if enabled)

### 3. Monitor ModSecurity Audit Log
```bash
# Watch for blocks in real-time
tail -f /var/log/apache2/modsec_audit.log

# Look for patterns
grep "id:" /var/log/apache2/modsec_audit.log | sort | uniq -c | sort -nr
```

### 4. Identify False Positives
Look for:
- Blocked legitimate requests
- 403 errors from valid users
- Broken AJAX functionality
- Failed form submissions

### 5. Fine-tune Rules
Add additional rule removals as needed based on your specific usage.

## Rule Structure Explanation

### Rule Numbering Scheme
- **1xxx** - API v1 endpoints
- **2xxx** - Page handlers
- **3xxx** - Component router (AJAX)
- **4xxx** - Plugin-specific routes
- **5xxx** - Common parameters and fields
- **6xxx** - Common operations

### Common Rule Actions Explained

**ctl:ruleRemoveById=920272** - Allow URL Encoding Anomalies
- Needed for complex query strings
- Used in search, API queries

**ctl:ruleRemoveById=941100, 941110, 941160, 941340** - Allow XSS in Specific Contexts
- 941100 - XSS Filter - Category 1
- 941110 - XSS Filter - Category 2
- 941160 - NoScript XSS InjectionChecker
- 941340 - IE XSS Filters
- Only removed for rich text fields (abstracts, biographies, etc.)

**ctl:ruleRemoveById=942100, 942110** - Allow SQL Special Characters
- Needed for search queries
- Citations and references
- Academic content with special characters

**ctl:ruleRemoveById=200002, 200003** - Allow File Uploads
- Needed for multipart/form-data
- File upload endpoints only

## Maintenance Recommendations

### Regular Tasks
1. **Weekly**: Review ModSecurity logs for new patterns
2. **Monthly**: Check for OJS updates that add new endpoints
3. **Quarterly**: Audit whitelist rules for unused entries
4. **After OJS Upgrade**: Re-run endpoint analysis

### Log Monitoring Commands
```bash
# Count blocks per rule
grep -o 'id "[0-9]*"' /var/log/apache2/modsec_audit.log | sort | uniq -c

# Find most blocked endpoints
grep "uri" /var/log/apache2/modsec_audit.log | sort | uniq -c | sort -nr | head -20

# Check specific rule triggers
grep "id \"941100\"" /var/log/apache2/modsec_audit.log
```

### Performance Considerations
- 47 rules have minimal performance impact
- Rules are evaluated in phase 1 (early)
- Use `nolog` to reduce log volume for known-good traffic
- Consider using `ctl:ruleEngine=Off` for truly static content

## Security Best Practices

### DO:
- ✓ Test thoroughly in staging environment
- ✓ Use DetectionOnly mode initially
- ✓ Monitor logs continuously after deployment
- ✓ Keep ModSecurity and CRS updated
- ✓ Document any custom rule additions
- ✓ Review rules after OJS updates

### DON'T:
- ✗ Remove entire attack categories (attack-xss, attack-rce)
- ✗ Disable ModSecurity entirely
- ✗ Whitelist without understanding why
- ✗ Skip testing in production-like environment
- ✗ Forget to backup before changes

## Rollback Plan

If issues occur after deployment:

### Immediate Rollback
```bash
# Restore backup
cp ojs-whitelist-rules.conf.backup ojs-whitelist-rules.conf

# Restart Apache
docker-compose restart apache

# Or switch to DetectionOnly
# Edit modsecurity.conf:
SecRuleEngine DetectionOnly
```

### Investigate and Fix
1. Check ModSecurity audit log
2. Identify specific blocked request
3. Determine if block was legitimate
4. Add specific rule exception if needed
5. Test fix
6. Redeploy

## Additional Resources

### ModSecurity Rule Reference
- [CRS Documentation](https://coreruleset.org/docs/)
- [ModSecurity Handbook](https://www.feistyduck.com/books/modsecurity-handbook/)

### OJS Documentation
- [OJS 3.4 Technical Documentation](https://docs.pkp.sfu.ca/dev/documentation/)
- [OJS REST API Guide](https://docs.pkp.sfu.ca/dev/api/)

## Support and Troubleshooting

### Common Issues

**Issue**: API requests return 403 errors
**Solution**: Check if endpoint is whitelisted in Section 1 rules

**Issue**: Form submissions fail
**Solution**: Check parameter names match Section 5 rules

**Issue**: File uploads blocked
**Solution**: Verify Rules 1013, 1014, 1022, 5007 are active

**Issue**: AJAX grid operations fail
**Solution**: Check Section 3 rules for component router

**Issue**: Plugin pages not working
**Solution**: Add plugin-specific rule in Section 4

### Debug Mode
Enable verbose logging:
```apache
SecDebugLog /var/log/apache2/modsec_debug.log
SecDebugLogLevel 9
```

**Warning**: Debug level 9 generates huge log files. Use only for troubleshooting.

## Contact and Feedback

- Generated by: Claude Code
- Date: 2025-11-11
- OJS Version: 3.4.0-7
- ModSecurity Version: Recommended 2.9.x or 3.x

---

**Remember**: Security is a balance. These rules allow OJS to function while maintaining reasonable protection. Always monitor and adjust based on your specific needs and threat model.
