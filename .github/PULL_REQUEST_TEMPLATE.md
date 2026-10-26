## Description

<!-- Provide a brief explanation of the change, motivation, and link to related issues. -->
Closes #<!-- issue number -->

### Type of Change
- [ ] Bug fix (non-breaking change fixing an issue)
- [ ] New feature (non-breaking change adding functionality)
- [ ] Breaking change (fix or feature causing existing functionality to not work as expected)
- [ ] Performance improvement
- [ ] Security hardening
- [ ] Documentation update
- [ ] Code refactor

---

## Architecture & Database Impact

- [ ] Does this PR modify Sequelize models or database migrations?
- [ ] Are all database mutations wrapped in a managed transaction (`db.sequelize.transaction`)?
- [ ] Have down-migrations been written and verified?
- [ ] Does this change affect session or JWT token structure?

---

## Security Verification Checklist

- [ ] All inputs validated via `express-validator` middleware
- [ ] CSRF tokens validated on all state-modifying requests (`POST`, `PUT`, `DELETE`, `PATCH`)
- [ ] No raw SQL query string interpolation (ORM parameterized queries only)
- [ ] No unescaped user-generated content rendered in SSR views (XSS prevention)
- [ ] Rate limiters and authorization guards applied to new endpoints

---

## Testing & Quality Checklist

- [ ] Manual end-to-end testing performed in local development environment
- [ ] No linter or syntax errors
- [ ] Browser console clean (no CSP violations or JS errors)
- [ ] Backward compatibility maintained for existing client sessions
