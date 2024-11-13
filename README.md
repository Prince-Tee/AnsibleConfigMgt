# Ansible Configuration Management Setup (Automate Project 7 to 10)

## Update the Name Tag on Jenkins EC2 Instance

1. Update the Name tag on your Jenkins EC2 Instance to `Jenkins-Ansible`.
    ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/Add%20Ansible%20to%20the%20Jenkins%20name%20tag%20on%20AWS.PNG)

---

## Install and Configure Ansible on EC2 Instance

### Step 1: Connect to Your EC2 Instance
1. In the AWS Console, go to **EC2** and locate your instance for Jenkins-Ansible.
2. Copy the **Public IP** of the instance.
3. Use SSH to connect to your instance:
   ```bash
   ssh -i path_to_your_key.pem ubuntu@your_instance_public_ip
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/ssh%20into%20your%20jenkins%20ansible%20server.PNG)

4. Once connected, update the instance:
   ```bash
   sudo apt update
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/update%20your%20jenkins%20ansible%20server.PNG)

### Step 2: Install Ansible
1. Install Ansible directly on the instance:
   ```bash
   sudo apt install ansible -y
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/install%20ansible%20on%20our%20jenkins%20server.PNG)

2. Check the Ansible version to confirm the installation:
   ```bash
   ansible --version
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/confirm%20the%20versio%20of%20ansible%20that%20is%20running.PNG)

---

## Configure Jenkins for Archiving Repository Content

### Step 1: Create a GitHub Repository
1. Go to your GitHub account and create a new repository named `ansible-config-mgt`.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/create%20a%20repositiory%20on%20github.PNG)

### Step 2: Set Up a New Freestyle Project in Jenkins
1. Open Jenkins and sign in at `http://your_instance_public_ip:8080`.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/sign%20in%20into%20jenkins%20using%20the%20public%20ip%20address.PNG)

2. Click **New Item** on the Jenkins dashboard, name the project `ansible`, select **Freestyle project**, and click **OK**.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/Select%20New%20Item%20on%20and%20name%20it%20ansible.PNG)

3. In the **Source Code Management** section, select **Git**.
4. Paste the URL of your `ansible-config-mgt` GitHub repository and add your GitHub credentials.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/in%20jekins%20choose%20git%20and%20put%20in%20the%20repository%20url%20and%20your%20github%20credential.PNG)

5. Allocate an **Elastic IP** to the Jenkins-Ansible server to maintain a consistent IP address.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/create%20elastic%20ip%20and%20associate%20it%20with%20our%20jenkins%20ansible%20server.PNG)

### Step 3: Set Up a GitHub Webhook to Trigger Jenkins Build
1. In GitHub, go to **Settings** for `ansible-config-mgt` repository.
2. Under **Webhooks**, click **Add webhook**.
3. Set **Payload URL** to `http://your_instance_public_ip:8080/github-webhook/`.
4. Set **Content type** to `application/json`.
5. Choose **Just the push event** to trigger the webhook.
6. Click **Add webhook**.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/configuring%20webhook.PNG)

---

## Configure Jenkins to Archive Build Artifacts

1. Go back to Jenkins and click **Configure** on the project.
2. Scroll down to **Post-build Actions**.
3. In **Post-build Actions**, select **Archive the artifacts**.
4. In **Files to archive**, type `**` to archive all files.
5. Check **GitHub hook trigger for GITScm polling**.
6. Click **Save** to finish configuring the Jenkins job.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/go%20to%20configure%20scroll%20to%20post%20build%20then%20archive%20activity.PNG)

---

## Test the Setup

1. In your `ansible-config-mgt` GitHub repository, edit `README.md` (e.g., add a new line).
2. Commit the changes directly to the master branch.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/go%20to%20github%20and%20make%20changes%20to%20your%20readme%20file.PNG)

3. In Jenkins, you should see a build triggered automatically.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/we%20have%20three%20build%20successful%20when%20we%20make%20changes%20to%20our%20readme%20file%20on%20github.PNG)

4. After the build completes, verify that the artifacts are archived in:
   ```bash
   ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
   ```
   Replace `<build_number>` with the specific build number.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/verify%20that%20the%20build%20artifacts%20are%20archived.PNG)

---

## Clone Repository to Jenkins-Ansible EC2 Instance

1. On the EC2 instance terminal, navigate to the desired directory.
2. Clone the `ansible-config-mgt` repository:
   ```bash
   git clone <repository-link>
   ```
   Replace `<repository-link>` with the GitHub repository URL.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/clone%20the%20repository%20on%20you%20jenkins%20ansible%20server.PNG)

---

## Prepare Development Environment with Visual Studio Code

1. Download and install **Visual Studio Code** on your local machine.
2. Open Visual Studio Code and configure Git integration:
   - Go to **View > Terminal**.
   - Navigate to the directory where the repository is located.
3. Clone the repository locally:
   ```bash
   git clone <repository-link>
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/start%20working%20on%20vscode%20after%20cloning%20it%20to%20your%20local%20environment.PNG)

---

## Set Up Branch Structure

1. Navigate to `ansible-config-mgt` on GitHub.
2. Go to **Branches** and click **New Branch**.
3. Name the branch descriptively, such as `feature/setup-inventory-playbooks`.

![ - **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/create%20a%20new%20branch%20on%20git.PNG)

4. Create and checkout the branch locally:
   ```bash
   git pull origin main
   git checkout -b feature/setup-inventory-playbooks
   ```
  ![screenshot](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/git%20checkout%20to%20the%20new%20branch%20created.PNG)

### Create Directory Structure for Ansible Files
1. Within the repository folder, create the following directories:
   ```bash
   mkdir playbooks
   mkdir inventory
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/create%20the%20folder%20playbook%20and%20inventory.PNG)

2. Create an empty playbook file named `common.yml` inside `playbooks`.
   ```bash
   cd playbooks
   touch common.yml
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/create%20comon%20yaml.PNG)

---

## Set Up Ansible Inventory

1. In Visual Studio Code, create 4 files in the `inventory` folder named `dev`, `staging`, `uat`, and `prod`.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/create%20dev%20staging%20uat%20and%20prod%20inside%20inventory.PNG)

2. Edit the **Development Inventory File (dev)** to define hosts:
   ```ini
   [nfs]
   <NFS-Server-Private-IP-Address> ansible_ssh_user=ec2-user

   [webservers]
   <Web-Server1-Private-IP-Address> ansible_ssh_user=ec2-user
   <Web-Server2-Private-IP-Address> ansible_ssh_user=ec2-user

   [db]
   <Database-Private-IP-Address> ansible_ssh_user=ec2-user 

   [lb]
   <Load-Balancer-Private-IP-Address> ansible_ssh_user=ubuntu
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/Edit%20the%20IInventory%20File%20dev%20AND%20ADD%20YOUR%20PRIVATE%20IP.PNG)

---

## Set Up SSH Agent and Import Private Key

1. Start the SSH Agent:
   ```bash
   eval ssh-agent -s
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/setting%20up%20ssh%20agent.PNG)

2. Navigate to `.ssh` directory and list files to locate the private key.
   ```bash
   cd ~/.ssh
   ls
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/searching%20for%20private%20key.PNG)

3. Add the private key:
   ```bash
   ssh-add ~/.ssh/id_rsa
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/add%20the%20pivate%20key%20and%20confirm%20that%20it%20has%20been%20added.PNG)


## SSH Connection Using `.pem` Key

If you encounter issues with `id_dsa` and receive a `Permission denied (public key)` error, follow these steps to use your `.pem` key instead:

1. **Start the SSH Agent**  
   ```bash
   eval $(ssh-agent -s)
   ```
   Output example:
   ```
   Agent pid 599
   ```

2. **Add the `.pem` Key**  
   Replace `<path-to-pem-file>` with the location of your `.pem` file (e.g., `C:\Users\PrinceTee\Downloads\lampproject.pem`).
   ```bash
   ssh-add "<path-to-pem-file>"
   ```

3. **Verify the Key Added**  
   List the keys added to the SSH agent to ensure your `.pem` file is included:
   ```bash
   ssh-add -l
   ```
   This should display the fingerprint of the `.pem` key.

   ![screenshot](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/using%20lammproject%20pem%20to%20create%20ssh%20agent.PNG)

4. **Test the SSH Connection**  
   Replace `<your-public-ip>` with the public IP address of your Jenkins-Ansible instance:
   ```bash
   ssh ubuntu@<your-public-ip>
   ```


## Update and Push Changes to GitHub

1. Add and commit the changes to Git:
   ```bash
   git add .
   git commit -m "Set up Ansible directory structure and development inventory"
   ```
2. Push changes to the feature branch:
   ```bash
   git push origin feature/setup-inventory-playbooks
   ```
   - **Screenshot**

---
## Connect to Jenkins-Ansible EC2 from Visual Studio Code

1. Open Visual Studio Code and install the **Remote - SSH** extension.
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/download%20remote%20ssh%20from%20vscode%20extension.PNG)

2. Press `F1`, type `Remote-SSH: Connect to Host`, and enter:
   ```plaintext
   ssh ubuntu@<your-public-ip>
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/add%20the%20jenkins%20ansible%20public%20ip%20to%20ssh%20in%20vscode.PNG)

   ![screenshot](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/after%20adding%20the%20ssh%20to%20be%20able%20to%20use%20the%20server%20directly%20from%20vscode.PNG)

3. Edit files directly on the server as needed.

## Configure and Test Ansible Playbook

1. Update `playbooks/common.yml` with the following configuration:
   ```yaml
   ---
   - name: update web, nfs, and db servers
     hosts: webservers, nfs, db
     become: yes
     tasks:
       - name: ensure wireshark is at the latest version
         yum:
           name: wireshark
           state: latest

   - name: update LB server
     hosts: lb
     become: yes
     tasks:
       - name: Update apt repo
         apt: 
           update_cache: yes

       - name: ensure wireshark is at the latest version
         apt:
           name: wireshark
           state: latest
   ```
![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/updating%20the%20common%20yml%20file.PNG)


## Additional Tasks in Ansible Playbook

In common.yml playbook you created in vscode you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.


1. Add the following additional tasks to create a directory, change the timezone, and run a shell script:
   ```yaml
   - name: additional tasks for all servers
     hosts: all
     become: yes
     tasks:
       - name: Create a directory
         file:
           path: /tmp/mydirectory
           state: directory

       - name: Create a file in the new directory
         file:
           path: /tmp/mydirectory/myfile.txt
           state: touch

       - name: Change timezone to UTC
         command: timedatectl set-timezone UTC

       - name: Run a shell script
         shell: /path/to/your/script.sh
   ```
  ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/updating%20the%20common%20yml%20file.PNG)


## Update GitHub with Your Code Changes

1. **Check the Status of Git Changes**  
   Run `git status` to see the modified files in your repository:
   ```bash
   git status
   ```

2. **Add Modified or Created Files**  
   Add only the specific files you’ve modified or created (e.g., `common.yml`):
   ```bash
   git add playbooks/common.yml
   ```

3. **Commit the Changes with a Descriptive Message**  
   Provide a clear and descriptive message about the changes made:
   ```bash
   git commit -m "Add common.yml playbook for server updates and configurations"
   ```

4. **Push the Branch to GitHub**  
   If you’re on a feature branch, push your branch to GitHub (e.g., `feature/setup-inventory-playbooks`):
   ```bash
   git push origin feature/setup-inventory-playbooks
   ```
![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/updating%20Update%20GitHub%20with%20Your%20Code%20Changes.PNG)

## Create a Pull Request on GitHub

1. On GitHub, go to your repository and create a pull request for your recent changes.
2. Provide a descriptive title and comment about the changes.

![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/merging%20the%20pull%20request.PNG)

3. Review and merge the pull request.

![screenshot](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/merged%20the%20pull%20request.PNG)

4. Pull the latest changes to your local master branch:
   ```bash
   git checkout master
   git pull origin master
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/git%20pull%20origin%20main.PNG)


## Verify Jenkins Automation (if Integrated with Jenkins)

1. **Jenkins Build Trigger**  
   If Jenkins is set to trigger a job on code changes, it will automatically start a build once your code is merged into the master branch.

   ![*(Screenshot)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/jenkins%20was%20automatically%20updated%20for%20build%20when%20we%20pushed%20changes%20to%20main%20branch.PNG)

2. **Confirm Build Artifacts on Jenkins**  
   - Log into Jenkins and navigate to your project.
   - After the job runs, confirm the build output is saved in `/var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/`.

   ![*(Screenshot)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/confirm%20the%20build%20is%20saved%20in%20the%20directory%20var%20jenkins.PNG)


## Connect to Your Ansible-Controlled Server from VSCode

1. **Open VSCode**  
   Start VSCode on your local machine.

2. **Connect to Your Remote Instance**  
   - Use the Remote - SSH extension in VSCode.
   - Press `F1`, type `Remote-SSH: Connect to Host...`, and select your server (e.g., `51.21.47.126`).  
   - Choose your operating system (Linux, Windows, or macOS). For this setup, select **Linux**.

   

3. **Handle SSH Permission Errors**  
   If you encounter a "permission denied (publickey)" error, update your SSH config file to include your SSH key:
   ```bash
   IdentityFile "C:\Users\Prince-Tee\Downloads\lampproject.pem"
   ```
   ![*(Screenshot)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/update%20the%20config%20file%20to%20the%20ssh%20key.PNG)

4. **Reconnect to the Host**  
   After updating, press `F1`, type `Remote-SSH: Connect to Host...`, and select the server. Confirm the SSH connection in the terminal and you will see that we can now connect to our server on vscode.

   ![*(Screenshots)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/connecting%20to%20our%20server%20from%20vscode.PNG)
   ![*(Screenshots)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/connection%20to%20our%20server%20on%20vscode.PNG)
   ![*(Screenshots)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/connection%20to%20server%20from%20vscode%20secured.PNG)

---

## Prepare the Ansible Environment

1. **Navigate to Your Ansible Directory**  
   - Once connected to the server through VSCode, open the terminal.
   - Go to your Ansible configuration directory where playbooks and inventory files are located:
     ```bash
     cd ~/ansible-config-mgt
     ```

   ![*(Screenshot)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/Prepare%20the%20Ansible%20Environment.PNG)

2. **Confirm Inventory and Playbook Locations**  
   Check that `inventory/dev.ini` and `playbooks/common.yml` are present:
   ```bash
   ls inventory/dev.ini playbooks/common.yml
   ```
    ![*(Screenshot)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/confirm%20the%20build%20is%20saved%20in%20the%20directory%20var%20jenkins.PNG)

   - If these files are not present, pull them from your repository:
     ```bash
     git pull origin main
     ```

   ![*(Screenshot)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/Prepare%20the%20Ansible%20Environment.PNG)



   - **Permission Issues**:  
     If you encounter permission issues, ensure your AWS security group allows SSH access from the Ansible server’s private IP, not `0.0.0.0/0`, for added security.
   
   ![*(Screenshot)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/update%20all%20teh%20servers%20with%20ansible%20private%20ip.PNG)

3. **Run the Ansible Playbook**  
   Run the playbook with the following command:
   ```bash
   ansible-playbook -i inventory/dev.ini playbooks/common.yml
   ```

   ![*(Screenshot)* ](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/run%20the%20final%20commandncommand.PNG)  
If you encounter the below error

It means Ansible is trying to manage a server that uses apt (Ubuntu/Debian package manager) with a task configured for yum (typically used by RHEL/CentOS systems). Since your playbook likely includes a package installation task for wireshark or similar, 
and it’s incorrectly assuming yum instead of apt. so change the content of your dev.ini file to the below to allow the download of the package

```bash
---
- name: update web, nfs, and db servers
  hosts: webservers, nfs, db
  become: yes
  tasks:
    - name: ensure wireshark is at the latest version on RHEL/CentOS servers
      yum:
        name: wireshark
        state: latest
      when: ansible_facts['pkg_mgr'] == "yum"

- name: update LB server
  hosts: lb
  become: yes
  tasks:
    - name: Update apt repository
      apt: 
        update_cache: yes
      when: ansible_facts['pkg_mgr'] == "apt"

    - name: ensure wireshark is at the latest version on Ubuntu/Debian servers
      apt:
        name: wireshark
        state: latest
      when: ansible_facts['pkg_mgr'] == "apt"
```
After that run the palybook again.

4. **Verify Installation**  
   You can confirm Wireshark installation on each server by running:
   ```bash
   which wireshark
   ```
   or
   ```bash
   wireshark --version
   ```

 **Conclusion**
 In this Ansible configuration management setup, we successfully automated the process of managing infrastructure through Jenkins and GitHub. The steps covered include setting up Ansible on the Jenkins EC2 instance, configuring Jenkins for automated builds and artifact archiving, and establishing a GitHub webhook to trigger builds upon code updates. The structured approach includes setting up a branch and directory organization within GitHub, creating a robust Ansible inventory, and configuring SSH for secure server access. Overall, this setup enables streamlined configuration management and efficient infrastructure provisioning, making it a practical foundation for further automation. 




