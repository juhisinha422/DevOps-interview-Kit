Successfully Deployed Nginx using Ansible on RHEL 🖥️📦

 task where automated the installation and setup of Nginx on a Red Hat-based EC2 instance using Ansible. This involved writing a simple YAML playbook to:

 ✅ Install the Nginx package

 ✅ Start and enable the Nginx service

🔧 Tools & Technologies Used:

 🔹 Ansible

 🔹 AWS EC2 (RHEL)

 🔹 YAML

 🔹 Linux CLI

📄 Here's a quick look at the playbook:
yaml

- name: Install and start Nginx on RHEL/CentOS
  hosts: web
  become: yes
  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: present

    - name: Start and enable Nginx
      service:
        name: nginx
        state: started
        enabled: yes

🔍 The execution was successful with no failures — a clean automation run!

 ![Image](https://github.com/user-attachments/assets/df7067b2-7255-4c00-878f-ea8525825d65)


 ![Image](https://github.com/user-attachments/assets/28822af1-af1c-4a79-a84a-7c6fe61582cf)
