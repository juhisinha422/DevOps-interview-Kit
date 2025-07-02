Ansible Playbook to Change Hostname!


Today I ran a simple Ansible playbook to change the hostname of a managed node.

📸 Screenshot attached:

 ✔️ Playbook used: h.yml

 ✔️ Target group: web

 ✔️ Task: Set hostname to (name)

🔹 Playbook Logic (YAML):
yaml

- name: change hostname
  hosts: web
  become: yes
  tasks:
    - name: set hostname
      hostname:
        name: (name)

🔹 Execution Command:

ansible-playbook -i inventory h.yml

💡 Lesson Learned:

 Earlier I faced a "no hosts matched" error — turns out I was using the wrong inventory input. Once corrected, everything worked as expected! Debugging and small wins like this really boost confidence while learning automation.

![Image](https://github.com/user-attachments/assets/feac6011-8aa1-474b-979d-ec982f6c645a)

