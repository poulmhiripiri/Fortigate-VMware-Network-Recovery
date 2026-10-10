# Publish to GitHub

Suggested repository name: `fortigate-vmware-lab-recovery`

Suggested description:

> Evidence-based recovery of a FortiGate VMware lab by reconstructing missing virtual networks from surviving firewall interfaces, routes and DHCP configuration.

Suggested topics: `fortigate`, `vmware-workstation`, `network-troubleshooting`, `disaster-recovery`, `homelab`, `network-engineering`, `incident-response`, `documentation`.

## GitHub web workflow

1. Extract the repository ZIP.
2. Sign in to GitHub and create a repository named `fortigate-vmware-lab-recovery`.
3. Choose visibility. The package includes lab addressing and selected original console screenshots; review them before choosing Public.
4. Open Add file → Upload files and upload the contents of the extracted folder, retaining docs, configs and evidence subdirectories. README.md must be at the repository root.
5. Commit with message: `Document FortiGate lab recovery from VMware network reset`.
6. Add the description and topics through the repository About settings.

This package does not create a remote GitHub repository or publish anything to your account. No GitHub connection was available during preparation.

## Optional Git CLI workflow

From the extracted repository directory, for an empty remote repository:

```bash
git init
git add .
git commit -m "Document FortiGate lab recovery from VMware network reset"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/fortigate-vmware-lab-recovery.git
git push -u origin main
```

Replace YOUR_USERNAME with your GitHub account name. Authenticate using your normal supported GitHub method; never place access tokens in documentation or repository files.

## Evidence handling

Browser bookmarks, local VM file paths and the Windows host account screenshot are intentionally excluded from the package. The selected console screenshots retain appliance identifiers; remove those before publishing if preferred. Screenshots are original evidence, not fabricated recovery output. No credentials or complete firewall backups are included.
