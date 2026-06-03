# LDAP RFC — draft PR structure

Use this checklist when opening the RFC pull request against [uyuni-rfc](https://github.com/uyuni-project/uyuni-rfc/).

## Repository layout (before merge)

```text
uyuni-rfc/
├── accepted/
│   ├── 00000-native-ldap-authentication.md    ← RFC body (no number until merge)
│   └── images/
│       ├── native-ldap-login-flow.png         ← export from mermaid / draw.io
│       ├── native-ldap-architecture.png
│       └── native-ldap-database-schema.png
└── (do not edit 00000-template.md)
```

Per [uyuni-rfc README](https://github.com/uyuni-project/uyuni-rfc/):

- Fork `https://github.com/uyuni-project/uyuni-rfc`
- Enable Git commit signing
- Copy content from `tooling/ldap-rfc-draft.md` → `accepted/00000-native-ldap-authentication.md`
- **Do not assign an RFC number** until the PR is ready to merge; then rename to the next free number in `master`
- Open PR and discuss
- Core team reviews weekly

## PR title and description (template)

**Title:** `RFC: Native LDAP authentication`

**Description:**

```markdown
## Summary

Native Java LDAP/AD authentication for Uyuni with optional JIT user provisioning
and LDAP group → Uyuni role mapping via revived `rhnUserExtGroup` tables.

## Motivation

Replace host-level-only LDAP (PAM/SSSD) and proxy-only external auth with
in-product configuration, testing, and group-aware RBAC mapping.

## Proof of work (community bonding)

- Auth flow mapped: `LoginController` → `LoginHelper` → `UserManager` → `UserImpl`
- LDAP POC: UnboundID SDK against local OpenLDAP fixture (`tooling/ldap-poc`, `tooling/dev-ldap`)
- Deploy loop validated on containerized Uyuni (`mvn package`, `mgrctl cp`, Tomcat restart)
- Design aligned with mentor feedback (login order, failure modes, table reuse, RBAC scope)

## Open design questions for reviewers

See **Unresolved questions** in the RFC. Proposed defaults are marked there;
feedback welcome on bind-password storage, external-group code reuse, and JIT default.

## Related links

- GSoC proposal: native LDAP authentication provider
- Mentor references: RBAC RFC #95, Spacewalk/IPA wiki, Satellite 6 LDAP video
- Exploration notes: `tooling/auth-flow-notes.md`, `tooling/auth-data-model-notes.md`, `tooling/ldap-design-notes.md` (personal fork / not in this PR)
```

## Files to copy from this workspace

| Source | Destination in uyuni-rfc fork |
| --- | --- |
| `tooling/ldap-rfc-draft.md` | `accepted/00000-native-ldap-authentication.md` |
| Diagram PNGs (when exported) | `accepted/images/native-ldap-*.png` |

Update image references in the RFC from mermaid blocks to:

```markdown
![login flow](images/native-ldap-login-flow.png)
```

## Diagram export (recommended)

The RFC includes mermaid sources. Before merge, export three PNGs (~1200px wide):

1. **Login flow** — Local → LDAP → PAM sequence
2. **Architecture** — `LdapServiceFactory` components
3. **Database schema** — `suseLdapAuthServer` + reused tables

Tools: [draw.io](https://drawsql.app/), VS Code Mermaid export, or `mmdc` (mermaid-cli).

## Review expectations (from accepted RFCs)

Reviewers will look for:

- Clear motivation and scope boundaries
- Detailed design with SQL, Java integration points, UI/API
- Honest drawbacks and alternatives
- Unresolved questions (design stays in RFC; packaging/licensing on Gitter)
- Phasing / iterations if the feature is large

## After merge

- Implementation PRs go to `uyuni-project/uyuni`, not `uyuni-rfc`
- Track work in a GitHub issue on the main Uyuni repo
- RFC number assigned at merge time (check latest in `accepted/`)

## Git commands (example)

```bash
git clone git@github.com:<your-user>/uyuni-rfc.git
cd uyuni-rfc
git checkout -b rfc-native-ldap-authentication
cp /path/to/tooling/ldap-rfc-draft.md accepted/00000-native-ldap-authentication.md
# add images under accepted/images/
git add accepted/
git commit -S -m "RFC: native LDAP authentication"
git push origin rfc-native-ldap-authentication
# open PR on GitHub against uyuni-project/uyuni-rfc master
```
