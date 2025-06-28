𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 𝐑𝐞𝐚𝐥-𝐓𝐢𝐦𝐞 𝐒𝐜𝐞𝐧𝐚𝐫𝐢𝐨: 𝐏𝐫𝐞𝐯𝐞𝐧𝐭𝐢𝐧𝐠 𝐀𝐜𝐜𝐢𝐝𝐞𝐧𝐭𝐚𝐥 𝐑𝐞𝐬𝐨𝐮𝐫𝐜𝐞 𝐃𝐞𝐥𝐞𝐭𝐢𝐨𝐧

🎯 𝐒𝐜𝐞𝐧𝐚𝐫𝐢𝐨 (𝐑𝐞𝐚𝐥-𝐖𝐨𝐫𝐥𝐝):

You’re managing critical cloud infrastructure using Terraform, including production databases, load balancers, and VPCs.
One day, a developer accidentally runs terraform apply after removing a resource block from the code.

𝐖𝐡𝐚𝐭 𝐡𝐚𝐩𝐩𝐞𝐧𝐬?

➡ Terraform sees that the resource is missing from code and plans to delete it from the cloud.
➡ You’re at risk of losing production data or infrastructure, just like that.

💡 𝐖𝐡𝐚𝐭 𝐈 𝐃𝐢𝐝 (𝐒𝐭𝐞𝐩-𝐛𝐲-𝐒𝐭𝐞𝐩):

1️⃣ Enabled prevent_destroy in resource blocks

𝒍𝒊𝒇𝒆𝒄𝒚𝒄𝒍𝒆 {
 𝒑𝒓𝒆𝒗𝒆𝒏𝒕_𝒅𝒆𝒔𝒕𝒓𝒐𝒚 = 𝒕𝒓𝒖𝒆
}

𝐓𝐡𝐢𝐬 𝐭𝐞𝐥𝐥𝐬 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦: 

“𝑬𝒗𝒆𝒏 𝒊𝒇 𝒔𝒐𝒎𝒆𝒐𝒏𝒆 𝒕𝒓𝒊𝒆𝒔 𝒕𝒐 𝒅𝒆𝒍𝒆𝒕𝒆 𝒕𝒉𝒊𝒔, 𝒔𝒕𝒐𝒑 𝒕𝒉𝒆 𝒂𝒑𝒑𝒍𝒚.”

2️⃣ Added checks in 𝑪𝑰/𝑪𝑫 𝒑𝒊𝒑𝒆𝒍𝒊𝒏𝒆

Used terraform plan in PRs and reviewed outputs carefully.

Blocked any changes with deletes unless explicitly approved.

3️⃣ Versioned 𝒔𝒆𝒏𝒔𝒊𝒕𝒊𝒗𝒆 𝒎𝒐𝒅𝒖𝒍𝒆𝒔

Database and networking modules were locked to tested versions.

This reduced accidental changes during unrelated updates.

🔥 𝐑𝐞𝐚𝐥 𝐈𝐧𝐜𝐢𝐝𝐞𝐧𝐭 𝐄𝐱𝐚𝐦𝐩𝐥𝐞:

A teammate accidentally deleted a security group block from the Terraform file.
During terraform apply, it was queued for deletion.

Thanks to prevent_destroy, Terraform stopped with an error — and nothing was destroyed.
We caught the mistake early and avoided downtime.

✨ 𝐊𝐞𝐲 𝐓𝐚𝐤𝐞𝐚𝐰𝐚𝐲:

In Terraform, deletion is automatic. Protection has to be intentional.

✅ Use 𝒑𝒓𝒆𝒗𝒆𝒏𝒕_𝒅𝒆𝒔𝒕𝒓𝒐𝒚 for critical resources.
✅ Always 𝒓𝒆𝒗𝒊𝒆𝒘 𝒑𝒍𝒂𝒏𝒔 — treat infrastructure changes like code changes.

![Image](https://github.com/user-attachments/assets/b27db746-3ea4-49eb-8e16-7dfc5a564b30)
