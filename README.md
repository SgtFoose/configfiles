# Config Files Repository

This repository contains configuration files for DVM systems.

## How to Add a Deploy Key

Deploy keys are SSH keys that grant access to a single repository. They are useful for CI/CD systems, automated deployments, or any service that needs read-only (or read-write) access to this repository.

### Step 1: Generate an SSH Key Pair

On your local machine or server, generate a new SSH key pair:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/configfiles_deploy_key
```

Or if your system doesn't support Ed25519, use RSA:

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -f ~/.ssh/configfiles_deploy_key
```

**Note:** Don't use a passphrase if this key will be used by automated systems.

This will create two files:
- `~/.ssh/configfiles_deploy_key` (private key - keep this secure!)
- `~/.ssh/configfiles_deploy_key.pub` (public key - this is what you'll add to GitHub)

### Step 2: Copy the Public Key

Display and copy the public key:

```bash
cat ~/.ssh/configfiles_deploy_key.pub
```

The output will look something like:
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJl3dIeudNqd0FGJE7hQzBXF5ZMKJ9JHVjcBV2xGXQzZ your_email@example.com
```

### Step 3: Add the Deploy Key to GitHub

1. Go to your repository on GitHub: https://github.com/SgtFoose/configfiles
2. Click on **Settings** (you need admin access to the repository)
3. In the left sidebar, click **Deploy keys**
4. Click **Add deploy key**
5. Give the key a descriptive title (e.g., "Production Server Deploy Key")
6. Paste the public key into the "Key" field
7. If you need write access, check **Allow write access**
8. Click **Add key**

### Step 4: Configure SSH on Your System

On the system that will use the deploy key, configure SSH to use it:

Create or edit `~/.ssh/config`:

```bash
Host github.com-configfiles
    HostName github.com
    User git
    IdentityFile ~/.ssh/configfiles_deploy_key
    IdentitiesOnly yes
```

### Step 5: Test the Connection

Test the SSH connection:

```bash
ssh -T git@github.com-configfiles
```

You should see a message like:
```
Hi SgtFoose/configfiles! You've successfully authenticated, but GitHub does not provide shell access.
```

### Step 6: Clone or Update Repository

**To clone the repository:**
```bash
git clone git@github.com-configfiles:SgtFoose/configfiles.git
```

**To update an existing repository to use the deploy key:**
```bash
cd /path/to/configfiles
git remote set-url origin git@github.com-configfiles:SgtFoose/configfiles.git
```

## Common Use Cases

### CI/CD Pipeline
Deploy keys are perfect for CI/CD systems like GitHub Actions, Jenkins, or GitLab CI that need to clone your repository.

### Production Server
Use a deploy key on your production server to pull the latest configuration files without requiring personal credentials.

### Automated Backups
Use a deploy key for scripts that automatically backup or sync configuration files.

## Security Best Practices

1. **Never commit private keys to the repository**
2. **Use separate deploy keys for different systems** - This allows you to revoke access to one system without affecting others
3. **Use read-only deploy keys when possible** - Only check "Allow write access" if absolutely necessary
4. **Rotate keys periodically** - Replace deploy keys every 6-12 months
5. **Remove unused deploy keys** - Regularly audit and remove old or unused keys from GitHub settings

## Troubleshooting

### Permission Denied Error
If you get a "Permission denied (publickey)" error:
1. Verify the deploy key is added to the repository on GitHub
2. Check that the SSH config is correct
3. Ensure the private key file has proper permissions: `chmod 600 ~/.ssh/configfiles_deploy_key`
4. Test the connection: `ssh -vT git@github.com-configfiles` (verbose mode)

### Wrong Repository or No Access
If you can connect but can't clone:
1. Verify you're using the correct repository URL
2. Check that the deploy key is added to the correct repository
3. If you need to push, ensure "Allow write access" is checked for the deploy key

## Additional Resources

- [GitHub Deploy Keys Documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys)
- [GitHub SSH Authentication](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
