# Why Your Emails Suddenly Go to Spam After Server Migration

Last month, a client called me in panic: "We migrated to a new server and now ALL our emails land in spam folders. Our business is dying!"

This scenario happens more often than you'd think. After 15+ years of managing email migrations, I've identified the exact reasons why this occurs and how to fix it fast.

## The Migration Reputation Reset

**Here's what actually happens:** When you migrate email servers, you're not just moving files - you're starting over with a blank reputation slate.

Email reputation works on TWO levels: **IP reputation** and **domain reputation**. Most admins focus only on IP, but domain reputation is equally critical.

### 1️⃣ IP Address Reputation Loss

**The Problem:** Your old server built trust over months/years. The new IP has zero history.

**What email providers see:**
- Unknown IP suddenly sending bulk emails
- No historical good behavior
- Suspicious pattern matching

**The Fix:**
```bash
# Check your new IP reputation immediately
dig +short myip.opendns.com @resolver1.opendns.com
# Test on: mxtoolbox.com, multirbl.valli.org
```

**Pro tip:** If possible, warm up the new IP gradually. Start with internal emails, then trusted contacts, before going full volume.

### 1️⃣.5 Domain Reputation Disruption

**What many miss:** Even with the same domain, sudden changes in email patterns hurt domain reputation.

**Gmail's domain scoring factors:**
- Consistent sending patterns (volume, timing, recipients)
- Authentication alignment (SPF, DKIM, DMARC matching)
- User engagement rates (opens, clicks, spam reports)

**Critical mistake:** Migrating during high-volume periods creates suspicious spikes.

**Domain reputation recovery:**
- Maintain consistent sending volumes
- Keep SPF/DKIM/DMARC properly aligned
- Monitor engagement metrics closely
- Avoid sudden pattern changes

### 2️⃣ DNS Records Inheritance Hell

**Most admins forget these critical DNS records:**

**Missing DKIM selectors:**
```bash
# Old server: mail._domainkey.yourdomain.com
# New server: mail2025._domainkey.yourdomain.com (different selector!)
```

**Mismatched SPF includes:**
```
# Before migration
v=spf1 ip4:OLD_IP include:_spf.google.com ~all

# After migration (WRONG)
v=spf1 ip4:NEW_IP include:_spf.google.com ~all
# Missing: the transition period overlap!
```

**The right approach:**
```
# During migration (both IPs valid)
v=spf1 ip4:OLD_IP ip4:NEW_IP include:_spf.google.com ~all
```

### 3️⃣ TLS Fingerprint Changes

**What happens:** Email providers track TLS certificate fingerprints. New server = new cert = suspicion flag.

**Gmail's algorithm thinks:** "This domain suddenly has different encryption. Possible hijack?"

**The solution:**
- Use the same certificate if possible
- Or migrate during low-traffic hours
- Monitor bounces closely for 48 hours

### 4️⃣ DKIM Key Rotation Mistakes

**Classic error:** Generating new DKIM keys without proper rotation.

**Wrong approach:**
1. Generate new DKIM keys
2. Update DNS
3. Start new server
4. Remove old DNS record

**Correct approach:**
1. Keep old DKIM key active
2. Add new DKIM selector to DNS
3. Configure new server with new selector
4. Wait 48 hours
5. Remove old key

### 5️⃣ Inherited IP Blacklist Issues

**The nightmare scenario:** Your new IP was previously used by spammers.

**Real case study:** Client's emails instantly blocked by Office365. The IP was on Microsoft's internal blacklist from 2 years ago.

**Investigation steps:**
```bash
# Check major blacklists
for bl in zen.spamhaus.org bl.spamcop.net b.barracudacentral.org; do
    dig +short NEW_IP.$bl
done
```

**If blacklisted:** Submit removal requests immediately. Some take 24-48 hours.

## My 7-Day Recovery Plan

**Day 1-2: Crisis Management**
- Audit all DNS records
- Check IP reputation on 10+ blacklists
- Configure proper DKIM rotation
- Enable detailed logging

**Day 3-4: Reputation Building**
- Send only to engaged recipients
- Monitor bounce rates religiously
- Authenticate all outbound mail
- Set up DMARC reporting

**Day 5-7: Scaling Up**
- Gradually increase volume
- Monitor spam folder placement
- Analyze DMARC reports
- Fine-tune configurations

## Quick Diagnostic Commands

When emails start hitting spam post-migration:

```bash
# 1. Check current IP reputation
echo "Your IP reputation score..."

# 2. Verify DNS propagation
dig TXT yourdomain.com | grep -i spf
dig TXT selector._domainkey.yourdomain.com

# 3. Test email authentication
# Send test email to mail-tester.com

# 4. Monitor logs
tail -f /var/log/mail.log | grep -i "reject\|defer\|spam"
```

## The 48-Hour Rule

**Critical insight:** Most reputation recovery happens within 48 hours IF you fix the underlying issues immediately.

I've seen domains go from 90% spam placement to 5% in just two days with the right approach.

## Prevention Is Better Than Cure

**Next time you migrate:**
- Plan DNS transition carefully
- Overlap old and new IP authorization for 72 hours  
- Test authentication flow before going live
- Have a rollback plan ready

**Bottom line:** Email migration isn't just moving files - it's reputation management. Treat it seriously, or face the spam folder consequences.

**Have you faced email reputation issues after migration? Share your experience below.**

#EmailMigration #Postfix #SystemAdmin #EmailDeliverability
