# Roles and permissions

Laravel is the authentication and authorization authority. The Next middleware guards page navigation; Laravel middleware/controller checks enforce API access. Users, roles, token hashes, submissions, reviews, notifications, and newsletter records are stored in Firestore through the Laravel API. Firestore rules deny browser access.

| Capability | Reader | Contributor | Editor | Admin |
|---|---:|---:|---:|---:|
| Read published WordPress articles | Yes | Yes | Yes | Yes |
| Manage own profile | Yes | Yes | Yes | Yes |
| Create/edit own drafts and submissions | No | Yes | Yes | Yes |
| Review submissions | No | No | Yes | Yes |
| Approve into WordPress pending status / publish | No | No | Yes | Yes |
| Manage users and roles | No | No | No | Yes |
| View editorial statistics and export newsletter | No | No | Yes | Yes |

Self-registration always assigns `READER`; privileged roles are assigned by an administrator. Submission API routes require Contributor/Editor/Admin. Review and statistics routes require Editor/Admin. User and role management requires Admin. These rules were inspected and exercised through API tests using an in-memory Firestore adapter; a live four-account staging test remains required.

Publishing requires server-side WordPress Application Password credentials. Firebase Authentication is not integrated and plays no role in authorization.
