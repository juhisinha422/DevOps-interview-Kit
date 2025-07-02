Understanding Ansible Architecture – Simplified View

 
simple diagram created to understand how its components work together.

🔹 3 Key Files in Ansible:

 1️⃣ Inventory – Lists target machines (Node1, Node2...)

 2️⃣ ansible.cfg – Configuration file that defines paths and permissions

 3️⃣ Playbook – YAML-based file that holds automation tasks (like adding users)

🔐 How it works:
The Server pushes tasks to Nodes using SSH

ansible.cfg manages authentication and settings

Users like rahul, pratik, and neha have access roles (web, db, file) based on their assigned tasks

⚙️ Key Configs:

inventory = (inventory path)  

privilege_escalation = (become Yes)

This visual approach really helps me reinforce the flow and structure of Ansible in real scenarios. One step closer to mastering Linux automation! 


![Image](https://github.com/user-attachments/assets/4a19bd13-8266-4a75-a019-70adead98b4c)
