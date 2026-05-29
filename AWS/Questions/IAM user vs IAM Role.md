Here’s your IAM prep content rewritten into a **clear, structured, interview‑ready format** so it’s easy to read and recall quickly:

---

## 1️⃣ Difference Between IAM User and IAM Role
| **IAM User** | **IAM Role** |
|--------------|--------------|
| Permanent identity | Temporary identity |
| Has username/password | No password |
| Used by humans | Used by AWS services/apps |
| Can have access keys | Uses temporary credentials |
| Example: Admin user | Example: EC2 accessing S3 |

**Easy Understanding**  
- **IAM User** → created for employees, admins, developers (e.g., `amit-user`)  
- **IAM Role** → used by EC2, Lambda, applications, cross‑account access (e.g., EC2 reads S3 bucket without storing access key)

---

## 2️⃣ Why IAM Role Preferred Over Access Keys?
- **Access Keys** → long‑term credentials, security risk if exposed  
- **IAM Roles** → temporary credentials, automatic rotation, better security  

**Example**  
❌ Bad Practice → Store access key inside application  
✅ Best Practice → Attach IAM role to EC2

---

## 3️⃣ Least Privilege Principle
- **Meaning**: Give only minimum required permissions  
- **Example**: If user only needs to restart EC2 → grant Start/Stop access only, not `AdministratorAccess`

---

## 4️⃣ Causes of AccessDenied Errors
| **Cause** | **Example** |
|-----------|-------------|
| Missing IAM permission | No S3 access |
| Explicit deny | Deny policy exists |
| Wrong bucket policy | S3 blocked |
| Missing trust relationship | Role cannot be assumed |
| SCP restriction | Organization policy block |
| Permission boundary | Restricted permissions |

---

## 5️⃣ How EC2 Accesses S3 Securely
- Uses **IAM Role**  
- Flow: `EC2 → IAM Role → Temporary Credentials → S3`  
- No need to store access key/secret key

---

## 6️⃣ Inline vs Managed Policy
| **Inline Policy** | **Managed Policy** |
|-------------------|--------------------|
| Attached to one user/role only | Reusable |
| Deleted with user | Independent |
| Harder to manage | Easier |
| One‑to‑one relation | One‑to‑many relation |

**Example**  
- Inline Policy → special permission for one user only  
- Managed Policy → same policy reused across many users

---

## 7️⃣ Trust Relationship
Defines **who can assume the role**.  

**Example EC2 Role Trust Policy**:
```json
{
  "Effect": "Allow",
  "Principal": {
    "Service": "ec2.amazonaws.com"
  }
}
```
➡️ Meaning: EC2 service can use this role

---

## 8️⃣ Explicit Deny
- In AWS, **Deny always overrides Allow**  
- Example: User has `AmazonS3FullAccess` but another policy says `Deny DeleteObject` → delete will fail

---

## 9️⃣ Rotating Access Keys
**Best Practice Steps**:  
1. Create new access key  
2. Update application  
3. Test  
4. Deactivate old key  
5. Delete old key  

**Path**: IAM → User → Security Credentials

---

## 🔟 Securing Root Account
**Best Practices**:  
- ✅ Enable MFA  
- ✅ Do not use root daily  
- ✅ Create IAM admin user  
- ✅ Delete root access keys  
- ✅ Use strong password  
- ✅ Monitor root activity with CloudTrail  
- ✅ Never share root credentials  

**Interview Answer**:  
Root account has full access to AWS services/resources, so it must be secured with MFA, strong passwords, and minimal usage. Daily tasks should be done using IAM users or roles instead of the root account.

---

✨ This version is **structured, concise, and interview‑friendly**. You can skim it like flashcards before interviews.  

Would you like me to also **convert this into a one‑page “cheat sheet” PDF style layout** (sections + highlights) so you can print or keep it handy?
