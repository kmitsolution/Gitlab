### **Creating a GPG Key on Windows Using Kleopatra**

In Windows, you can use the **GPG4Win** tool, which includes **Kleopatra**, a graphical user interface (GUI) for managing your GPG keys. If you're more comfortable using the command line, you can also use GPG via the command prompt or PowerShell.

Here’s a detailed breakdown of how to create a GPG key pair on Windows using **GPG command-line tools**:

---

### **Step 1: Install GPG (GPG4Win)**
To get started, you need to install GPG on your Windows machine.

1. **Download GPG4Win**:
   - Visit the [GPG4Win website](https://gpg4win.org/).
   - Download the latest version of GPG4Win, which contains the necessary GPG tools, including Kleopatra.
   
2. **Install GPG4Win**:
   - Run the installer and follow the prompts.
   - During the installation, make sure to install **GpgOL**, **GpgEX**, and **Kleopatra** (for managing your GPG keys).

---

### **Step 2: Open Command Prompt (or PowerShell) as Administrator**
You will need administrator privileges to generate the GPG key pair.

1. **Open Command Prompt**:
   - Search for "Command Prompt" or "PowerShell" in the Windows Start menu.
   - Right-click on the app and select **Run as Administrator**.

---

### **Step 3: Generate the GPG Key Pair**
Once you have opened the Command Prompt or PowerShell as an administrator, you can proceed to generate the key pair.

1. **Run the command** to generate the GPG key:
   ```bash
   gpg --full-generate-key
   ```

2. **Follow the prompts**:
   - **Key Type**: Select **RSA and RSA** (default option). Press **Enter** to confirm.
   - **Key Size**: Enter `4096` for a secure key size (recommended). Press **Enter**.
   - **Key Expiration**: You can specify how long the key should be valid (e.g., 1 year) or leave it blank to make the key never expire. Press **Enter**.
   - **User ID**: Enter your **real name**, **email address**, and an optional comment (e.g., `GPG Key for GitLab`).
     - Example: `John Doe <john.doe@example.com>`
   - **Passphrase**: Choose a **strong passphrase** to protect your private key. You will be asked to re-enter the passphrase for confirmation.

After entering all the information, GPG will generate your key pair.

---

### **Step 4: Retrieve Your Key ID**
Once the key is generated, you will need the **Key ID** to configure Git and GitLab. To find your GPG key ID, run:

```bash
gpg --list-secret-keys --keyid-format LONG
```

You’ll see an output like this:

```
/home/your_user/.gnupg/secring.gpg
------------------------------
sec   4096R/ABCD1234EFGH5678 2023-03-30 [expires: 2024-03-30]
uid                          John Doe <john.doe@example.com>
ssb   4096R/1234567890ABCDEF 2023-03-30
```

Note the key ID after `4096R/`, which is `ABCD1234EFGH5678` in this case.

---

### **Step 5: Export Your Public Key**
To add your GPG key to GitLab, you need to export the **public key**.

1. Run the following command to export your public key in ASCII format:
   ```bash
   gpg --armor --export ABCD1234EFGH5678 > public_key.txt
   ```
   Replace `ABCD1234EFGH5678` with your actual GPG Key ID.

2. This will create a file called **public_key.txt** containing your public key.

---

### **Step 6: Export Your Private Key (Optional)**
**Important**: The private key should **never** be shared. However, if you want to create a backup of your private key, you can export it.

1. Run the following command to export your private key:
   ```bash
   gpg --armor --export-secret-keys ABCD1234EFGH5678 > private_key.txt
   ```
   Replace `ABCD1234EFGH5678` with your actual GPG Key ID.
   
2. This will create a **private_key.txt** file that contains your private key. Store this file securely!

---

### **Step 7: Add Your GPG Public Key to GitLab**
Now that you have your public key, add it to GitLab to enable commit signing.

1. **Log in to GitLab** and navigate to **User Settings** (click on your profile picture in the top-right corner and select **Settings**).
   
2. In the left sidebar, click on **GPG Keys** under **Preferences**.

3. **Paste your public key**:
   - Open the **public_key.txt** file you exported earlier.
   - Copy the entire content (including the `-----BEGIN PGP PUBLIC KEY BLOCK-----` and `-----END PGP PUBLIC KEY BLOCK-----` lines).
   - Paste it into the **Key** field in GitLab.

4. **Click "Add key"** to complete the process.

---

### **Step 8: Configure Git to Use Your GPG Key**
Finally, you need to tell Git to use your GPG key when signing commits.

1. **Set your GPG key** for Git to use:
   ```bash
   git config --global user.signingkey ABCD1234EFGH5678
   ```
   Replace `ABCD1234EFGH5678` with your actual GPG Key ID.

2. To sign your commits automatically, run:
   ```bash
   git config --global commit.gpgSign true
   ```

This ensures that all your future commits will be signed with your GPG key.

---

### **Step 9: Verify the GPG Key in GitLab**
To confirm that your commit is signed correctly, create a commit and push it to GitLab.

1. Create a commit in your Git repository:
   ```bash
   git add .
   git commit -m "Test commit with GPG signature"
   ```

2. Push the commit to your GitLab repository:
   ```bash
   git push origin main
   ```

3. In GitLab, navigate to your repository, and you should see that your commit is signed with your GPG key. GitLab will show a **verified** label next to your commit if the signature is valid.

<img width="749" alt="image" src="https://github.com/user-attachments/assets/0a322bd9-18dc-42d1-a87e-a5e673e8db15" />


---

### **Important Notes**
- **Never share your private key**. The private key should be kept secure, as it can be used to sign your commits and authenticate your identity.
- **Backup your private key**: Ensure you have a backup of your private key in a secure location.
- **Use a strong passphrase**: Choose a unique and strong passphrase to protect your GPG key.

By following these steps, you'll successfully generate and configure a GPG key for use with GitLab on Windows.
