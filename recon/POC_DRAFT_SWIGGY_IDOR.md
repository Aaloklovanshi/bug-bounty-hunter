# PoC Draft: Broken Access Control (IDOR) on Swiggy MCP Servers

## 🛑 MISSION: Bug Bounty (Whale Hunter)
**Target:** Swiggy MCP (Builders Club)
**Severity:** Critical
**Vulnerability Class:** Broken Access Control / Insecure Direct Object Reference

## 🔍 Vulnerable Endpoints & Tools
1. **Server:** `https://mcp.swiggy.com/food`
   - **Tool:** `place_food_order`
   - **Parameter:** `addressId` (Mandatory)
2. **Server:** `https://mcp.swiggy.com/im` (Instamart)
   - **Tool:** `checkout`
   - **Parameter:** `addressId` (Mandatory)

## 🛠️ Reproduction Steps (Hermes-Elite Methodology)
1. **Auth:** Complete OAuth 2.1 (PKCE) to obtain `mcp:tools` scope.
2. **Recon:** 
   - Call `get_addresses` to list the authenticated user's address IDs.
   - Note a legitimate ID: `addr_OWNER_123`.
3. **Exploitation (Hypothesis):**
   - Perform a cross-account IDOR attempt by calling `place_food_order` or `checkout` using an `addressId` harvested from external logs or previous leaks (e.g., `addr_VICTIM_456`).
   - If the request succeeds (returns `{"success": true}`), it indicates that the Swiggy MCP backend does not validate if the `addressId` belongs to the authenticated `$SWIGGY_TOKEN` owner.
4. **Validation:**
   - Call `get_food_order_details` with the resulting `orderId` to verify if victim's PII is exposed.

## 💥 Elite Impact Assessment
- **Unauthorized Financial Transactions:** Forcing orders on victim's saved cards.
- **Bulk PII Data Breach:** Traversing `addressId` space to harvest delivery locations.
- **Business Logic Bypass:** Swiggy MCP beta limits (₹1000) might be bypassable if session context is confused.

---
*Status: Ready for final verification and submission to security@swiggy.in*
*Hermes-Elite Proactive Monitoring: ACTIVE*
