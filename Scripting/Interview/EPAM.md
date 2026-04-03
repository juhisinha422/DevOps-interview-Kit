# AWS IAM Questions & Answers (4+ Years Experience)

---

## Write a code to list the users who has accessed the keys over 90 days ?

```bash
#!/bin/bash

current_date=$(date +%s)

for user in $(aws iam list-users --query 'Users[*].UserName' --output text); do

  for key in $(aws iam list-access-keys --user-name $user --query 'AccessKeyMetadata[*].AccessKeyId' --output text); do

    create_date=$(aws iam list-access-keys \
      --user-name $user \
      --query "AccessKeyMetadata[?AccessKeyId=='$key'].CreateDate" \
      --output text)

    key_date=$(date -d "$create_date" +%s)

    age_days=$(( (current_date - key_date) / 86400 ))

    if [ "$age_days" -gt 90 ]; then
      echo "User: $user | AccessKey: $key | Age: $age_days days"
    fi

  done
done
```

---

## Write a code to remove the * in the permissions from iam users. ?

```bash
#!/bin/bash

for user in $(aws iam list-users --query 'Users[*].UserName' --output text); do

  for policy in $(aws iam list-user-policies --user-name $user --query 'PolicyNames[*]' --output text); do

    policy_doc=$(aws iam get-user-policy \
      --user-name $user \
      --policy-name $policy \
      --query 'PolicyDocument' \
      --output json)

    if echo "$policy_doc" | grep -q '"\*"' ; then
      echo "User: $user | Policy: $policy contains wildcard (*)"

      # Uncomment below to delete the policy (use carefully)
      # aws iam delete-user-policy --user-name $user --policy-name $policy

    fi

  done
done
```

---
