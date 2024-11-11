---

# Self-Study Documentation: Jenkins and Ansible Setup & Integration with VSCode

## Overview

This documentation outlines the steps to verify Jenkins automation, connect to an Ansible-controlled server from VSCode, and set up the Ansible environment for managing infrastructure configurations.

---

### Step 1: Jenkins Automation Verification

1. **Jenkins Build Trigger**:  
   If Jenkins is set up to trigger builds on code changes, it will automatically start a job once your code merges into the `main` branch.

   *Verify Output Location:*  
   After the Jenkins job completes, verify build artifacts are saved in the directory:
   ```
   /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
   ```
   ![- **Screenshot**](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/verify%20that%20the%20build%20artifacts%20are%20archived.PNG)

    ![*(Screenshot)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/confirm%20the%20build%20is%20saved%20in%20the%20directory%20var%20jenkins.PNG)

### Step 2: Connect to Ansible-Controlled Server from VSCode

1. **Open VSCode & Connect to Remote Instance**:
   - Launch VSCode on your local machine.
   - Use the `Remote - SSH` extension to connect to your remote server:
     - Press `F1` > Type `Remote-SSH: Connect to Host...` > Select `51.21.47.126`.
     - Choose `Linux` as the server OS.

2. **Resolve Connection Issues**:
   If you encounter the error:  
   ```
   "Could not establish connection to '51.21.47.126': permission denied (publickey)"
   ```
   update your SSH config file by adding the identity file line:
   ```  
   IdentityFile "C:\Users\Prince-Tee\Downloads\lampproject.pem"  
   ```
   ![*(Screenshot)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/update%20the%20config%20file%20to%20the%20ssh%20key.PNG)

3. **Verify Successful Connection**:  
   After the update, re-initiate the connection. Confirm connection success via the terminal output in VSCode.
   

---

### Step 3: Prepare the Ansible Environment

1. **Navigate to the Ansible Directory**:
   - Once connected, open the terminal in VSCode.
   - Navigate to the Ansible configuration directory:
     ```  
     cd ~/ansible-config-mgt  
     ```

2. **Confirm Inventory and Playbook Files**:
   - Verify the presence of `inventory/dev.ini` and `playbooks/common.yml` by listing the files:
     ```  
     ls inventory/dev.ini playbooks/common.yml  
     ```
    ![*(Screenshot)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/confirm%20the%20build%20is%20saved%20in%20the%20directory%20var%20jenkins.PNG)  
   - If missing, update files by running:
     ```  
     git pull origin main  
     ```
    ![*(Screenshot)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/confirm%20the%20build%20is%20saved%20in%20the%20directory%20var%20jenkins.PNG)

3. **Run the Ansible Playbook**:
   Execute the playbook to apply configurations:
   ```  
   ansible-playbook -i inventory/dev.ini playbooks/common.yml  
   ```
   ![*(Screenshot)* ](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/run%20the%20final%20commandncommand.PNG) 

4. **Troubleshoot Permissions**:
   - If permission issues arise, verify Inbound Rules in AWS:
     - Update security group rules to allow SSH access specifically from your Ansible server’s private IP.
     ![*(Screenshot)*](https://github.com/Prince-Tee/AnsibleConfigMgt/blob/main/screenshot%20from%20my%20local%20env/update%20all%20teh%20servers%20with%20ansible%20private%20ip.PNG)

---

### Conclusion

This setup process ensures efficient configuration management by integrating Jenkins for automated builds and VSCode with SSH for seamless server management. Ansible enables consistent infrastructure provisioning and configuration deployment.

