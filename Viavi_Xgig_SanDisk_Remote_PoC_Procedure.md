Revision2
Updated
# Viavi Xgig SAS/SATA Remote PoC — Setup & Bring-Up Procedure

**Customer:** SanDisk
**Engagement:** Remote access to Viavi-hosted Xgig SAS/SATA analyzer/exerciser rig for automation PoC
**Owner (Viavi SE):** Eli Urshanski
**Status:** DRAFT — placeholders below must be confirmed before use
**Last updated:** 2026-07-19

---

## 0. Placeholders to confirm before execution

| Placeholder | Description | Value |
|---|---|---|
| `<LAB_RIG_ID>` | Which Xgig SAS/SATA chassis/exerciser is reserved | TBD |
| `<BASTION_HOST>` | Jump host / VPN gateway address for remote access | TBD |
| `<BASTION_PORT>` | SSH port on bastion (if non-default) | TBD |
| `<AUTH_METHOD>` | SSH key vs password, and how it's delivered to SanDisk | TBD |
| `<ACCOUNT_EXPIRY>` | Date the temporary SanDisk account is revoked | TBD |
| `<CHASSIS_IP>` | Xgig chassis IP/hostname reachable from the bastion | TBD |
| `<CHASSIS_API_PORT>` | API port on the chassis | TBD |
| `<LICENSE_TYPE>` | Dongle / license server, and confirmation it covers API automation | TBD |
| `<API_PACKAGE_VERSION>` | Xgig API package + required Python version | TBD |
| `<VPN_CLIENT>` | VPN client SanDisk must install (if applicable) | TBD |
| `<ACCESS_AGREEMENT_REF>` | NDA / access agreement doc reference, if required | TBD |

Do not send this document to SanDisk until every placeholder above is filled in and verified.

---

## 1. Viavi-side preparation (before SanDisk is looped in)

1. **Reserve the remote lab rig**
   - Confirm `<LAB_RIG_ID>` is free for the PoC window.
   - Confirm the rig is networked with a routable IP/port for remote access (not bench-local only).

2. **Provision remote access**
   - Request a temporary SSH/VPN account via Viavi IT/lab ops scoped to `<LAB_RIG_ID>` only.
   - Capture: `<BASTION_HOST>`, `<BASTION_PORT>`, `<AUTH_METHOD>`, `<ACCOUNT_EXPIRY>`.
   - Confirm the account is scoped/least-privilege (SanDisk should not see other tenants' rigs).

3. **Confirm licensing**
   - Verify `<LICENSE_TYPE>` is current and explicitly covers the API/automation feature set (not just GUI operation).

4. **Confirm API package + version**
   - Record `<API_PACKAGE_VERSION>` and the Python version it targets, so SanDisk can match dependencies client-side.

5. **Prepare a known-good sample script**
   - A simple "hello world" script (e.g., link-up check or basic trace capture) to validate the chassis + API before SanDisk starts writing their own automation.

6. **Paperwork**
   - Confirm whether `<ACCESS_AGREEMENT_REF>` (NDA / external access agreement) is required and signed before credentials are issued.

---

## 2. What SanDisk needs on their end

- SSH client (OpenSSH or PuTTY).
- `<VPN_CLIENT>` installed and configured, if the lab sits behind a VPN.
- Matching Python version + Xgig API client package (`<API_PACKAGE_VERSION>`) installed locally or on the jump host.
- Outbound SSH (and VPN, if used) cleared through their corporate firewall/proxy — flag this early, it is the most common blocker.
- Any signed access agreement (`<ACCESS_AGREEMENT_REF>`) completed before credentials are sent.

---

## 3. Connection & bring-up sequence

1. SanDisk connects to VPN (if required) using `<VPN_CLIENT>`.
2. SSH to the bastion:
   ```
   ssh -p <BASTION_PORT> <sandisk_user>@<BASTION_HOST>
   ```
3. From the bastion, reach the Xgig chassis (second hop — chassis is typically not directly internet-facing):
   ```
   ssh <chassis_user>@<CHASSIS_IP>
   ```
   or connect via the Xgig API client to `<CHASSIS_IP>:<CHASSIS_API_PORT>`.
4. Verify chassis reachability — run the chassis status/version query over the API and confirm license (`<LICENSE_TYPE>`) and firmware match expectations.
5. Run the prepared known-good script from Step 1.5 — confirms API auth, chassis link, and capture pipeline end-to-end.
6. Only after Step 5 passes, hand SanDisk their own test scripts and let them begin writing automation against the confirmed-working baseline.

---

## 4. Validation checklist (sign-off before handing to SanDisk)

- [ ] Bastion SSH access tested from an external network (not just Viavi internal)
- [ ] Chassis reachable from bastion via SSH/API
- [ ] License confirmed valid for automation/API use
- [ ] Known-good sample script runs clean end-to-end
- [ ] Temporary account expiry (`<ACCOUNT_EXPIRY>`) set and documented
- [ ] Access agreement (`<ACCESS_AGREEMENT_REF>`) signed, if required

---

## 5. Next steps

- Fill in all placeholders in Section 0.
- Re-run Section 4 checklist personally before sending credentials.
- Draft and send onboarding email to SanDisk with prerequisites (Section 2) and connection steps (Section 3).
