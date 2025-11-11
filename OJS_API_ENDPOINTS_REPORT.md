# OJS 3.4.0-7 API Endpoints and Request Paths - Comprehensive Report

## Executive Summary

This report identifies all API endpoints, page handlers, and AJAX request paths in OJS 3.4.0-7 that require mod_security whitelist rules. OJS uses three routing mechanisms:

1. **API Router** - RESTful API endpoints (`/api/v1/*`)
2. **Page Router** - Traditional page handlers (`/index.php/[context]/page/operation`)
3. **Component Router** - AJAX/Grid components (`/index.php?component=...&op=...` or `/index.php/[context]/$$$call$$$/...`)

---

## 1. API v1 Endpoints (`/api/v1/*`)

### 1.1 Submissions API
- **Path**: `/api/v1/submissions`
- **Path**: `/api/v1/submissions/{id}`
- **Path**: `/api/v1/submissions/{id}/publications`
- **Path**: `/api/v1/submissions/{id}/publications/{publicationId}`
- **Path**: `/api/v1/submissions/{id}/files` (File uploads)
- **Path**: `/api/v1/_submissions` (Backend/Admin)
- **Methods**: GET, POST, PUT, DELETE
- **Handler**: `api/v1/submissions/SubmissionHandler.php`
- **Status**: Partially whitelisted (Rule 8 covers publications)

### 1.2 Issues API
- **Path**: `/api/v1/issues`
- **Path**: `/api/v1/issues/{id}`
- **Path**: `/api/v1/issues/current`
- **Methods**: GET, POST, PUT, DELETE
- **Handler**: `api/v1/issues/IssueHandler.php`
- **Status**: NOT whitelisted

### 1.3 Users API
- **Path**: `/api/v1/users`
- **Path**: `/api/v1/users/{id}`
- **Methods**: GET, POST, PUT, DELETE
- **Handler**: `api/v1/users/UserHandler.php`
- **Status**: NOT whitelisted

### 1.4 Contexts API (Journals)
- **Path**: `/api/v1/contexts`
- **Path**: `/api/v1/contexts/{id}`
- **Methods**: GET, POST, PUT, DELETE
- **Handler**: `api/v1/contexts/ContextHandler.php`
- **Status**: NOT whitelisted

### 1.5 Announcements API
- **Path**: `/api/v1/announcements`
- **Path**: `/api/v1/announcements/{id}`
- **Methods**: GET, POST, PUT, DELETE
- **Handler**: `api/v1/announcements/index.php`
- **Status**: NOT whitelisted

### 1.6 DOIs API
- **Path**: `/api/v1/dois`
- **Path**: `/api/v1/dois/{id}`
- **Path**: `/api/v1/_dois` (Backend/Admin)
- **Methods**: GET, POST, PUT, DELETE
- **Handler**: `api/v1/dois/DoiHandler.php`
- **Status**: NOT whitelisted

### 1.7 Statistics API
- **Path**: `/api/v1/stats/editorial`
- **Path**: `/api/v1/stats/publications`
- **Path**: `/api/v1/stats/issues`
- **Path**: `/api/v1/stats/sushi` (COUNTER statistics)
- **Methods**: GET
- **Handler**: `api/v1/stats/*Handler.php`
- **Status**: NOT whitelisted

### 1.8 Email Templates API
- **Path**: `/api/v1/emailTemplates`
- **Path**: `/api/v1/emailTemplates/{id}`
- **Methods**: GET, POST, PUT, DELETE
- **Handler**: `api/v1/emailTemplates/index.php`
- **Status**: NOT whitelisted

### 1.9 Mailables API
- **Path**: `/api/v1/mailables`
- **Methods**: GET
- **Handler**: `api/v1/mailables/index.php`
- **Status**: NOT whitelisted

### 1.10 Temporary Files API
- **Path**: `/api/v1/temporaryFiles`
- **Methods**: GET, POST, DELETE
- **Handler**: `api/v1/temporaryFiles/index.php`
- **Status**: NOT whitelisted (File upload endpoint)

### 1.11 Library Files API
- **Path**: `/api/v1/_library`
- **Methods**: GET, POST, PUT, DELETE
- **Handler**: `api/v1/_library/index.php`
- **Status**: NOT whitelisted (File management)

### 1.12 Site API
- **Path**: `/api/v1/site`
- **Methods**: GET, PUT
- **Handler**: `api/v1/site/index.php`
- **Status**: NOT whitelisted

### 1.13 Vocabs API
- **Path**: `/api/v1/vocabs`
- **Methods**: GET
- **Handler**: `api/v1/vocabs/index.php`
- **Status**: NOT whitelisted

### 1.14 Highlights API
- **Path**: `/api/v1/highlights`
- **Path**: `/api/v1/highlights/{id}`
- **Methods**: GET, POST, PUT, DELETE
- **Handler**: `api/v1/highlights/index.php`
- **Status**: NOT whitelisted

### 1.15 Institutions API
- **Path**: `/api/v1/institutions`
- **Path**: `/api/v1/institutions/{id}`
- **Methods**: GET, POST, PUT, DELETE
- **Handler**: `api/v1/institutions/index.php`
- **Status**: NOT whitelisted

### 1.16 Jobs API
- **Path**: `/api/v1/jobs`
- **Methods**: GET
- **Handler**: `api/v1/jobs/index.php`
- **Status**: NOT whitelisted

### 1.17 Email API (Backend)
- **Path**: `/api/v1/_email`
- **Methods**: GET, POST
- **Handler**: `api/v1/_email/index.php`
- **Status**: NOT whitelisted

### 1.18 Payments API (Backend)
- **Path**: `/api/v1/_payments`
- **Methods**: GET, PUT
- **Handler**: `api/v1/_payments/BackendPaymentsSettingsHandler.php`
- **Status**: NOT whitelisted

### 1.19 Upload Public File API
- **Path**: `/api/v1/_uploadPublicFile`
- **Methods**: POST
- **Handler**: `api/v1/_uploadPublicFile/index.php`
- **Status**: NOT whitelisted (File upload endpoint)

---

## 2. Page Handlers (`/index.php/[context]/page/operation`)

### 2.1 Workflow Pages
- **Path**: `/index.php/*/workflow/*`
- **Handler**: `pages/workflow/WorkflowHandler.php`
- **Operations**: index, submission, externalReview, editorial, production, access
- **Status**: NOT whitelisted

### 2.2 Submission Pages
- **Path**: `/index.php/*/submission/*`
- **Handler**: `pages/submission/SubmissionHandler.php`
- **Operations**: wizard, saved
- **Status**: NOT whitelisted

### 2.3 Author Dashboard
- **Path**: `/index.php/*/authorDashboard/*`
- **Handler**: `pages/authorDashboard/AuthorDashboardHandler.php`
- **Status**: NOT whitelisted

### 2.4 Reviewer Pages
- **Path**: `/index.php/*/reviewer/*`
- **Handler**: `pages/reviewer/ReviewerHandler.php`
- **Operations**: submission, step, saveStep, showDeclineReview
- **Status**: NOT whitelisted

### 2.5 Management Pages
- **Path**: `/index.php/*/management/*`
- **Handler**: `pages/management/SettingsHandler.php`
- **Operations**: settings, access, context, website, workflow, distribution
- **Status**: NOT whitelisted (includes custom-block-manager - Rule 7)

### 2.6 Issue Management
- **Path**: `/index.php/*/manageIssues/*`
- **Handler**: `pages/manageIssues/ManageIssuesHandler.php`
- **Status**: NOT whitelisted

### 2.7 Article Pages
- **Path**: `/index.php/*/article/*`
- **Handler**: `pages/article/ArticleHandler.php`
- **Operations**: view, download
- **Status**: NOT whitelisted

### 2.8 Issue Pages
- **Path**: `/index.php/*/issue/*`
- **Handler**: `pages/issue/IssueHandler.php`
- **Operations**: current, archive, view
- **Status**: NOT whitelisted

### 2.9 User Pages
- **Path**: `/index.php/*/user/*`
- **Handler**: `pages/user/UserHandler.php`
- **Operations**: register, login, profile, changePassword, lostPassword
- **Status**: NOT whitelisted

### 2.10 Search Pages
- **Path**: `/index.php/*/search/*`
- **Handler**: `pages/search/SearchHandler.php`
- **Operations**: index, search, authors
- **Status**: NOT whitelisted (Rule 3 covers search parameters)

### 2.11 Payment Pages
- **Path**: `/index.php/*/payment/*`
- **Handler**: `pages/payment/PaymentHandler.php`
- **Status**: NOT whitelisted

### 2.12 Payments Handler
- **Path**: `/index.php/*/payments/*`
- **Handler**: `pages/payments/PaymentsHandler.php`
- **Status**: NOT whitelisted

### 2.13 Stats Pages
- **Path**: `/index.php/*/stats/*`
- **Handler**: `pages/stats/StatsHandler.php`
- **Operations**: publications, editorial, users, context
- **Status**: NOT whitelisted

### 2.14 Information Pages
- **Path**: `/index.php/*/information/*`
- **Handler**: `pages/information/InformationHandler.php`
- **Operations**: contact, about, submissions, authors
- **Status**: NOT whitelisted

### 2.15 Sitemap Pages
- **Path**: `/index.php/*/sitemap/*`
- **Handler**: `pages/sitemap/SitemapHandler.php`
- **Status**: NOT whitelisted

### 2.16 DOIs Pages
- **Path**: `/index.php/*/dois/*`
- **Handler**: `pages/dois/DoisHandler.php`
- **Status**: NOT whitelisted

### 2.17 OAI Pages
- **Path**: `/index.php/*/oai`
- **Handler**: `pages/oai/OAIHandler.php`
- **Operations**: index (OAI-PMH protocol)
- **Status**: NOT whitelisted

### 2.18 Gateway Pages
- **Path**: `/index.php/*/gateway/*`
- **Handler**: `pages/gateway/GatewayHandler.php`
- **Operations**: plugin
- **Status**: NOT whitelisted

### 2.19 About Pages
- **Path**: `/index.php/*/about/*`
- **Handler**: `pages/about/AboutHandler.php`
- **Status**: NOT whitelisted

### 2.20 Catalog Pages
- **Path**: `/index.php/*/catalog/*`
- **Handler**: `pages/catalog/*`
- **Status**: NOT whitelisted

### 2.21 Decision Pages
- **Path**: `/index.php/*/decision/*`
- **Handler**: `pages/decision/*`
- **Status**: NOT whitelisted

### 2.22 Index/Homepage
- **Path**: `/index.php/*/index/*`
- **Handler**: `pages/index/IndexHandler.php`
- **Status**: NOT whitelisted

---

## 3. Component Router (AJAX/Grid Handlers)

### 3.1 Grid Controllers (56+ handlers in lib/pkp/controllers/grid/)
- **Pattern**: `/index.php?component=grid.*&op=*`
- **Pattern**: `/index.php/[context]/$$$call$$$/grid/*/operation`
- **Examples**:
  - `/grid/toc/TocGridHandler`
  - `/grid/issues/BackIssueGridHandler`
  - `/grid/issues/FutureIssueGridHandler`
  - `/grid/articleGalleys/ArticleGalleyGridHandler`
  - `/grid/issueGalleys/IssueGalleyGridHandler`
  - `/grid/subscriptions/*GridHandler`
  - `/grid/users/reviewer/ReviewerGridHandler`
  - `/grid/settings/sections/SectionGridHandler`
  - `/grid/navigationMenus/*`
- **Status**: NOT whitelisted

### 3.2 Modal Controllers
- **Pattern**: `/index.php?component=modals.*&op=*`
- **Pattern**: `/index.php/[context]/$$$call$$$/modals/*/operation`
- **Example**: `/modals/publish/*`
- **Status**: NOT whitelisted

### 3.3 Tab Controllers
- **Pattern**: `/index.php?component=tab.*&op=*`
- **Pattern**: `/index.php/[context]/$$$call$$$/tab/*/operation`
- **Examples**:
  - `/tab/pubIds/*`
  - `/tab/workflow/*`
- **Status**: NOT whitelisted

### 3.4 API File Controller
- **Pattern**: `/index.php?component=api.file.*&op=*`
- **Pattern**: `/index.php/[context]/$$$call$$$/api/file/*/operation`
- **Handler**: `controllers/api/file/*`
- **Status**: NOT whitelisted (File operations)

---

## 4. Plugin-Specific Routes

### 4.1 Citation Style Language Plugin
- **Path**: `/index.php/*/citationStyleLanguage/*`
- **Handler**: `plugins/generic/citationStyleLanguage/pages/CitationStyleLanguageHandler.php`
- **Status**: NOT whitelisted

### 4.2 Static Pages Plugin
- **Path**: `/index.php/*/staticPages/*`
- **Handler**: `plugins/generic/staticPages/StaticPagesHandler.php`
- **Component**: `plugins.generic.staticPages.controllers.grid.StaticPageGridHandler`
- **Status**: NOT whitelisted

### 4.3 Custom Block Manager Plugin
- **Path**: `/index.php/*/management/settings/website`
- **Component**: `plugins.generic.customBlockManager.controllers.grid.CustomBlockGridHandler`
- **Status**: Partially whitelisted (Rule 7 - custom-block-manager path)

### 4.4 ORCID Profile Plugin
- **Path**: `/index.php/*/orcidapi/*`
- **Handler**: `plugins/generic/orcidProfile/OrcidProfileHandler.php`
- **Status**: NOT whitelisted

### 4.5 JATS Template Plugin
- **Path**: `/index.php/*/jatsTemplate/*`
- **Handler**: `plugins/generic/jatsTemplate/JatsTemplateDownloadHandler.php`
- **Status**: NOT whitelisted

---

## 5. Special Patterns and Parameters

### 5.1 Common Query Parameters (Already Whitelisted)
- Rule 3: `searchPhrase`, `orderBy`, `orderDirection`, `count`, `offset`, `isEnabled`, `isActive`
- Rule 4: `biography`, `abstract`, `coverage`, `rights`, `source`, `subjects`
- Rule 5: `citations`, `references`, `citationsRaw`
- Rule 6: `csrfToken`, `token`, `_token`

### 5.2 File Upload Parameters
- **Parameters**: `uploadedFile`, `temporaryFileId`, `fileStage`
- **Status**: NOT whitelisted

### 5.3 Common Operations (op=)
- `fetchGrid`, `addItem`, `editItem`, `deleteItem`, `updateItem`, `saveSequence`
- `fetch`, `save`, `delete`, `update`
- **Status**: NOT whitelisted

---

## 6. Summary of Gaps in Current Whitelist

### Currently Whitelisted (8 rules):
1. `/api/v1/` endpoints (partial)
2. JSON content type
3. Search parameters
4. Rich text fields
5. Citation fields
6. CSRF tokens
7. Custom block manager
8. Submissions/publications API (very broad - removes all XSS/RCE rules)

### Major Gaps:
1. **Most API v1 endpoints** - Only submissions/publications partially covered
2. **All page handlers** - None of the 20+ page routes covered
3. **All component/AJAX routes** - Grid, modal, tab controllers not covered
4. **File upload endpoints** - temporaryFiles, _uploadPublicFile, _library
5. **Plugin routes** - ORCID, static pages, JATS, CSL not covered
6. **Component router paths** - `$$$call$$$` pattern not covered
7. **Grid operations** - fetchGrid, addItem, etc. not covered
8. **OAI-PMH endpoint** - `/oai` not covered
9. **Statistics endpoints** - API and page routes not covered
10. **DOI management** - API and page routes not covered

---

## 7. Recommended Whitelist Strategy

### Option A: Broad Approach (Easier but Less Secure)
- Whitelist entire `/index.php/` path structure
- Whitelist all `/api/v1/` endpoints
- Whitelist component router pattern (`$$$call$$$`)

### Option B: Targeted Approach (More Secure - RECOMMENDED)
- Create specific rules for each API endpoint category
- Create rules for each page handler category
- Create specific rules for component router operations
- Maintain granular control over security exceptions

### Option C: Hybrid Approach
- Broad rules for read-only operations (GET requests)
- Targeted rules for write operations (POST, PUT, DELETE)
- Specific rules for file uploads and sensitive operations

---

## 8. Security Considerations

### High-Risk Endpoints (Require Careful Whitelisting):
1. **File uploads**: `/api/v1/temporaryFiles`, `/api/v1/_uploadPublicFile`, `/api/v1/_library`
2. **HTML content**: Custom blocks, email templates, rich text fields
3. **Code injection risks**: Plugin management, settings pages
4. **XSS risks**: User profiles, submission metadata, announcements
5. **SQL injection risks**: Search operations, grid filters

### Current Rule 8 Concern:
Rule 8 removes ALL XSS and RCE protection for submissions/publications API:
```
ctl:ruleRemoveByTag=attack-rce,\
ctl:ruleRemoveByTag=attack-xss,\
```
This is extremely broad and should be reconsidered.

---

## Generated by Claude Code
Date: 2025-11-11
OJS Version: 3.4.0-7
Analysis Method: Source code exploration
