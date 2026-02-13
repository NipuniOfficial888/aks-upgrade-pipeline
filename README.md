# AKS Minor Version Upgrade Automation Pipeline

## 📦 Package Contents

This package contains everything you need to set up automated AKS minor version upgrades with approval gates and notifications.

### Files Included:

1. **aks-minor-upgrade-pipeline.yml** - The main Azure DevOps pipeline YAML file
2. **AKS-UPGRADE-SETUP-GUIDE.md** - Complete setup and configuration instructions
3. **AKS-UPGRADE-QUICK-REFERENCE.md** - Quick reference for day-to-day operations

---

## 🎯 What This Pipeline Does

### Automated Monthly Checks
- Runs every **1st Saturday of the month at 2 AM UTC**
- Checks for new Kubernetes minor versions
- Only proceeds if a **minor version upgrade** is available (patches are already automated)

### Smart Approval Process
- Sends email notification when upgrade is available
- Requires approval from **2 approvers** (nipuni.w@aventude.com, iran.u@aventude.com)
- **2-day timeout** for approval decision
- Shows current vs. target version details

### Comprehensive Health Checks
- **Pre-upgrade:** Cluster health, node status, version availability
- **During upgrade:** Control plane → Node pools (sequential)
- **Post-upgrade:** Version verification, node health, pod validation

### Email Notifications
- 📧 Pre-upgrade reminder (upgrade available)
- 📧 Approval request
- 📧 Upgrade started confirmation
- 📧 Completion status (success or failure)

---

## 🚀 Quick Start (5 Minutes)

### Step 1: Upload Pipeline to Azure DevOps
1. Go to: https://dev.azure.com/kommuneforlaget/BK
2. Navigate to **Pipelines** → **Pipelines**
3. Click **"New Pipeline"**
4. Choose your repo location
5. Upload `aks-minor-upgrade-pipeline.yml`
6. Click **"Save"** (not "Save and run")

### Step 2: Verify Service Connection
1. Check that `BK_Stage_new_cluster_creation` has permissions
2. Go to **Project Settings** → **Service connections**
3. Click **"Verify"** on your service connection

### Step 3: Configure Notifications
1. Go to your **User Settings** → **Notifications**
2. Enable notifications for:
   - Build completes
   - Manual validation pending

### Step 4: Test It!
1. Click **"Run pipeline"** manually
2. Watch it check for upgrades
3. If upgrade available, test the approval process

**That's it!** The pipeline will now run automatically every month.

---

## 📋 Cluster Details

| Property | Value |
|----------|-------|
| **Cluster Name** | Aks-BK-Stage-NorwayEast-001 |
| **Resource Group** | rg-kf-bk-stage-norwayeast-001 |
| **Subscription** | KF-Glow |
| **Location** | Norway East |
| **Current Version** | 1.33.6 |
| **Environment** | BK-Stage |
| **Service Connection** | BK_Stage_new_cluster_creation |

---

## 🗓️ Schedule

### When Does It Run?
- **Day:** 1st Saturday of each month
- **Time:** 2:00 AM UTC (3:00 AM CET / 4:00 CEST)
- **Duration:** ~30-60 minutes total

### Next Scheduled Runs (2026):
- March 7 (Sat)
- April 4 (Sat)
- May 2 (Sat)
- June 6 (Sat)
- July 4 (Sat)

---

## 📧 Approvers

The following people will receive approval requests:
- **Nipuni Wickramasinghe** - nipuni.w@aventude.com
- **Iran Udayanga** - iran.u@aventude.com

**Both approvers must approve** within **2 days** for the upgrade to proceed.

---

## 🔍 Pipeline Flow

```
┌─────────────────────────────────────────────────────────────┐
│  Scheduled Trigger (1st Saturday @ 2 AM UTC)                │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│  Stage 1: Pre-Upgrade Check (~5 min)                        │
│  ✓ Check current Kubernetes version                         │
│  ✓ List available upgrades                                  │
│  ✓ Determine if minor upgrade available                     │
│  ✓ Verify cluster health                                    │
│  ✓ Check node pool status                                   │
│  ✓ Send pre-upgrade notification email                      │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
              Is Minor Upgrade
               Available? ───NO──> ✅ Pipeline Completes
                     │
                    YES
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│  Stage 2: Approval Gate (up to 2 days)                      │
│  ⏸  Wait for manual approval                                │
│  📧 Send approval request to:                               │
│     - nipuni.w@aventude.com                                 │
│     - iran.u@aventude.com                                   │
│  ⏱  Timeout: 2 days                                         │
└────────────────────┬────────────────────────────────────────┘
                     │
         ┌───────────┴───────────┐
         │                       │
      APPROVED                REJECTED/TIMEOUT
         │                       │
         │                       ▼
         │                 ❌ Pipeline Cancelled
         │
         ▼
┌─────────────────────────────────────────────────────────────┐
│  Stage 3: Upgrade Execution (~30-40 min)                    │
│  📧 Send "upgrade started" notification                     │
│  ⚙️  Upgrade control plane                                  │
│  ⚙️  Upgrade node pools (sequential)                        │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│  Stage 4: Post-Upgrade Validation (~5 min)                  │
│  ✓ Verify cluster version                                   │
│  ✓ Check node pool health                                   │
│  ✓ Validate system pods                                     │
│  ✓ Check node status                                        │
│  📧 Send success/failure notification                       │
└────────────────────┬────────────────────────────────────────┘
                     │
         ┌───────────┴───────────┐
         │                       │
      SUCCESS                  FAILED
         │                       │
         ▼                       ▼
   ✅ Completed            ❌ Failed (auto-rollback)
```

---

## 📚 Documentation Guide

### For Initial Setup:
👉 **Read:** `AKS-UPGRADE-SETUP-GUIDE.md`
- Complete step-by-step setup instructions
- Email configuration options
- Service connection setup
- Troubleshooting guide

### For Day-to-Day Operations:
👉 **Read:** `AKS-UPGRADE-QUICK-REFERENCE.md`
- Quick commands and actions
- Email notification flow
- Common scenarios
- Troubleshooting commands

### For Customization:
👉 **Edit:** `aks-minor-upgrade-pipeline.yml`
- Change schedule (cron expression)
- Adjust approval timeout
- Modify health checks
- Add custom notifications

---

## ⚠️ Important Notes

### What This Pipeline DOES:
✅ Checks for minor version upgrades monthly
✅ Requires manual approval before upgrading
✅ Upgrades control plane and node pools
✅ Validates cluster health before and after
✅ Sends email notifications at each stage

### What This Pipeline DOES NOT Do:
❌ Patch upgrades (already automated weekly)
❌ Automatic upgrades without approval
❌ Backup cluster data (add if needed)
❌ Test application compatibility (manual process)

### Best Practices:
1. Always review Kubernetes release notes before approving
2. Check for breaking changes
3. Approve during maintenance windows when possible
4. Monitor applications after upgrade
5. Document any issues for future reference

---

## 🔧 Customization Options

### Change Schedule
Edit the cron expression in the YAML file:
```yaml
schedules:
- cron: "0 2 * * 6"  # Current: Every Saturday at 2 AM UTC
```

Common alternatives:
- `0 2 1 * *` - 1st day of month at 2 AM
- `0 2 * * 0` - Every Sunday at 2 AM
- `0 2 1 */3 *` - Quarterly (every 3 months)

### Change Approvers
Edit the variable in the YAML file:
```yaml
variables:
  approverEmails: 'nipuni.w@aventude.com;iran.u@aventude.com'
```

### Change Timeout
Edit the timeout in the approval stage:
```yaml
timeoutInMinutes: 2880  # 2 days = 2880 minutes
```

---

## 🆘 Support & Troubleshooting

### Common Issues:

**Pipeline doesn't run on schedule:**
- Check pipeline is not paused
- Verify branch name matches (should be `main`)
- Check repository has recent commits

**Approval email not received:**
- Check spam folder
- Verify email addresses in pipeline variables
- Configure Azure DevOps notification settings

**Upgrade fails:**
- Check pipeline logs for error details
- Verify service connection has permissions
- Check cluster health in Azure Portal
- AKS automatically rolls back failed upgrades

### Get Help:
1. Check pipeline logs in Azure DevOps
2. Review the troubleshooting section in `AKS-UPGRADE-SETUP-GUIDE.md`
3. Contact approvers: nipuni.w@aventude.com, iran.u@aventude.com

---

## 📊 Success Criteria

After setup, you should see:
- ✅ Pipeline appears in Azure DevOps Pipelines
- ✅ Schedule is configured (visible in triggers)
- ✅ First manual test run completes successfully
- ✅ Approvers receive test notifications
- ✅ Documentation is accessible to the team

---

## 🔄 Extending to Other Environments

Currently configured for: **BK-Stage**

To add more environments (Production, Form+, etc.):

**Option 1: Multiple Pipelines** (Recommended)
- Duplicate this pipeline
- Update cluster details for each environment
- Run each independently

**Option 2: Single Pipeline with Matrix** (Advanced)
- Use pipeline matrix strategy
- Loop through multiple clusters
- Requires YAML modification

See `AKS-UPGRADE-SETUP-GUIDE.md` for detailed instructions.

---

## 📝 Changelog

### Version 1.0 (February 11, 2026)
- Initial release
- Monthly schedule (1st Saturday)
- Approval gates with 2 approvers
- Pre and post-upgrade health checks
- Email notifications
- Configured for BK-Stage environment

---

## 📞 Contact

**Pipeline Maintainers:**
- Nipuni Wickramasinghe - nipuni.w@aventude.com
- Iran Udayanga - iran.u@aventude.com

**Azure DevOps Project:** BK
**Organization:** kommuneforlaget

---

## ✅ Quick Checklist

Before going live, make sure you've:

- [ ] Uploaded pipeline YAML to Azure DevOps
- [ ] Verified service connection works
- [ ] Configured email notifications
- [ ] Run a successful test
- [ ] Added calendar reminders for 1st Saturday
- [ ] Documented in Confluence
- [ ] Notified the team
- [ ] Read the setup guide
- [ ] Bookmarked the quick reference guide

---

**🎉 You're all set! The pipeline will now automatically check for updates and notify you when action is needed.**

For detailed instructions, see: **AKS-UPGRADE-SETUP-GUIDE.md**
For quick reference, see: **AKS-UPGRADE-QUICK-REFERENCE.md**
