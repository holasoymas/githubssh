# Automating SSH Key Authentication for GitHub: A Step-by-Step Guide

## Introduction

If you've encountered issues with GitHub pushes due to expired Personal Access Tokens (PATs) or frequent authentication prompts, this guide provides a detailed solution. Instead of relying on PATs for HTTPS-based authentication, this guide will show you how to set up SSH keys for secure, password-free authentication when pushing and pulling from GitHub. Additionally, we'll automate the SSH agent setup so that you don't need to manually run commands every time you start a new terminal session.

Using SSH keys has many benefits over HTTPS, including enhanced security, permanent authentication (no expiration), and convenience. Follow this guide to set up SSH for GitHub and eliminate the need to repeatedly authenticate using passwords or tokens.

---

## Table of Contents

1. [Why Use SSH Authentication?](#why-use-ssh-authentication)
2. [Steps to Set Up SSH Authentication for GitHub](#steps-to-set-up-ssh-authentication-for-github)
   - [Generating an SSH Key](#generating-an-ssh-key)
   - [Adding the SSH Key to GitHub](#adding-the-ssh-key-to-github)
   - [Setting Up the SSH Agent](#setting-up-the-ssh-agent)
   - [Automating SSH Agent Setup (Optional)](#automating-ssh-agent-setup-optional)
   - [Verifying SSH Connection](#verifying-ssh-connection)
   - [Switching Remote URL from HTTPS to SSH](#switching-remote-url-from-https-to-ssh)
3. [Why SSH Over HTTPS?](#why-ssh-over-https)

---

## Why Use SSH Authentication?

GitHub supports multiple authentication methods, with HTTPS and SSH being the most common. Here's why SSH is the better choice for most developers:

### 1. **Security**

SSH keys provide strong encryption, ensuring that your credentials are never transmitted over the internet in an unencrypted format. This makes SSH more secure than passwords, which can be intercepted if not used correctly.

### 2. **No Expiry**

Unlike Personal Access Tokens (PATs) that expire every 3 months, SSH keys remain valid indefinitely, unless you revoke them. This removes the need for you to manually renew authentication tokens.

### 3. **No Repeated Prompts**

Once you've configured SSH authentication, you don't need to repeatedly enter your username and password (or PAT) every time you interact with GitHub. SSH allows you to authenticate once and enjoy seamless push/pull operations without further prompts.

### 4. **Convenience**

SSH authentication automatically handles your credentials in the background, reducing friction and making your workflow smoother, especially when dealing with multiple repositories.

---

## Steps to Set Up SSH Authentication for GitHub

### Generating an SSH Key

First, you'll need to create a new SSH key pair. This consists of a **private key** (which stays on your machine) and a **public key** (which you’ll upload to GitHub).

1. **Generate a new SSH key** by running the following command in your terminal:

   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```

   - The `-t ed25519` option creates a new key using the Ed25519 algorithm, which is more secure and efficient than RSA.
   - The `-C` option adds a comment to the key, typically your email address, for identification purposes.

2. **Choose a location** to save your SSH key (default is `~/.ssh/id_ed25519`). If you’ve previously created an SSH key, you can specify a different name or overwrite the existing one.

3. Optionally, set a **passphrase** for added security (not mandatory).

---

### Adding the SSH Key to GitHub

Once you’ve generated the SSH key pair, the next step is to add the **public key** to your GitHub account.

1. **View your public SSH key**:

   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```

   This command will output the contents of the public key. Copy this entire output (it starts with `ssh-ed25519`).

2. **Add your SSH key to GitHub**:

   - Go to **GitHub > Settings > SSH and GPG Keys**.
   - Click **New SSH Key**.
   - Paste your public key into the provided field and give it a title (e.g., "My new SSH Key").
   - Click **Add SSH Key**.

   or click here ***https://github.com/settings/keys***

---

### Setting Up the SSH Agent

The next step is to ensure that your SSH private key is loaded into the SSH agent, which manages your keys for you.

1. **Start the SSH agent**:

   ```bash
   eval "$(ssh-agent -s)"
   ```

   - This starts the SSH agent, which will manage your SSH keys in memory.

2. **Add your SSH private key to the agent**:

   ```bash
   ssh-add ~/.ssh/id_ed25519
   ```

   - This command adds your SSH private key (`id_ed25519`) to the SSH agent so that it can be used for authentication.

---

### Automating SSH Agent Setup (Optional)

To avoid manually running the above two commands every time you start a terminal session, you can automate this process.

This guide will help you automate the SSH key management for GitHub to avoid manually running `ssh-add` every time you push to or pull from your GitHub repository. By configuring your `~/.ssh/config` file, your SSH key will be automatically added to the SSH agent, making your GitHub operations smoother and more efficient.

1. Open the SSH config file for editing:

   ```bash
   nano ~/.ssh/config
   ```

2. Add the following configuration to the file:
   ```bash
   Host * # or a hostname eg : `Host github.com`
    AddKeysToAgent yes
    UseKeychain yes       # Only for macOS
    IdentityFile ~/.ssh/id_ed25519 # path to your ssh key
   ```

---

### Verifying SSH Connection

Now that everything is set up, verify that your SSH connection to GitHub is working.

1. Run the following command:

   ```bash
   ssh -T git@github.com
   ```

2. You should see a message like:

   ```bash
   Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
   ```

   - **Explanation**: This confirms that your SSH key is correctly set up and that you can authenticate with GitHub using SSH.

---

### Switching Remote URL from HTTPS to SSH

If your Git repository was previously using HTTPS for pushing and pulling, you’ll want to switch to SSH to avoid future authentication prompts.

1. **Change the remote URL** from HTTPS to SSH with the following command:

   ```bash
   git remote set-url origin git@github.com:<username>/<repository>.git
   ```

   - **Why**: This updates your repository’s configuration to use the SSH URL (`git@github.com:<username>/<repository>.git`) instead of the HTTPS URL (`https://github.com/<username>/<repository>.git`).
   - Once you’ve done this, Git will use your SSH key for authentication rather than prompting you for a password or PAT.

---

## Why SSH Over HTTPS?

While HTTPS is simple and widely used, SSH has several advantages:

1. **More Secure**: SSH uses public-key cryptography, making it more secure than basic username/password authentication over HTTPS.
2. **No Expiry**: SSH keys don’t expire like Personal Access Tokens, meaning you don’t have to worry about renewing them every few months.
3. **Convenience**: Once set up, SSH allows you to push and pull from GitHub without needing to type your credentials each time.

---
