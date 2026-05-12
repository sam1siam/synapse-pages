---
title: Payment confirmed — Synapse for X
permalink: /thank-you/
---

# 🎉 Payment confirmed

<div id="syn-loading" style="padding: 1em; background: #f0f8ff; border-radius: 6px;">
  <p><strong>Fetching your license details…</strong></p>
  <p style="font-size: 0.9em; color: #555;">If this takes more than 60 seconds, payment is probably still being confirmed on-chain. Refresh this page in a minute.</p>
</div>

<div id="syn-details" style="display: none;">
  <p>You're now on the <strong><span id="syn-tier"></span></strong> plan (<span id="syn-period"></span> subscription, expires <span id="syn-expires"></span>).</p>

  <p>The <strong>Synapse extension auto-activates this license within 60 seconds</strong> if you have the side panel's Upgrade tab open. If it didn't auto-activate, enter your key manually:</p>

  <ol>
    <li>Open the <strong>Synapse</strong> side panel in Chrome (click the toolbar icon).</li>
    <li>Click <strong>Options</strong>.</li>
    <li>Go to the <strong>💎 Upgrade</strong> tab.</li>
    <li>Expand <strong>"Have a license key already? Activate manually"</strong>.</li>
    <li>Paste this key:
      <div style="margin: 0.5em 0; padding: 0.75em; background: #f5f5f5; border: 1px solid #ddd; border-radius: 4px; font-family: monospace; font-size: 1.1em; word-break: break-all;">
        <span id="syn-key"></span>
        <button id="syn-copy" style="margin-left: 1em; padding: 0.3em 0.8em; cursor: pointer; font-family: inherit; font-size: 0.85em;">Copy</button>
      </div>
    </li>
    <li>Click <strong>Activate license</strong>.</li>
  </ol>

  <p style="font-size: 0.9em; color: #555; margin-top: 2em;">
    Save this key somewhere safe — you'll need it to reactivate the extension if you reinstall Chrome or move to another computer.
  </p>
</div>

<div id="syn-error" style="display: none; padding: 1em; background: #fff5f5; border-radius: 6px; border-left: 4px solid #d33;">
  <p><strong>Couldn't fetch your license details.</strong></p>
  <p id="syn-error-msg" style="font-family: monospace; font-size: 0.9em;"></p>
  <p>Email <a href="mailto:asam4r@gmail.com">asam4r@gmail.com</a> with your order ID and we'll send your key manually.</p>
</div>

<p style="font-size: 0.85em; color: #888; margin-top: 3em;">Order: <code id="syn-order"></code></p>

<script>
  (function() {
    const BACKEND = "https://synapse-license.asam4r.workers.dev"
    const params = new URLSearchParams(window.location.search)
    const orderId = params.get("order")
    const orderEl = document.getElementById("syn-order")
    if (orderId) orderEl.textContent = orderId
    if (!orderId) {
      document.getElementById("syn-loading").style.display = "none"
      document.getElementById("syn-error").style.display = "block"
      document.getElementById("syn-error-msg").textContent = "No order ID in the URL — did you arrive here directly?"
      return
    }

    let attempts = 0
    const MAX_ATTEMPTS = 30 // ~2.5 min @ 5s interval

    function fmtDate(ms) {
      try { return new Date(ms).toLocaleDateString(undefined, { year: "numeric", month: "long", day: "numeric" }) }
      catch (e) { return new Date(ms).toISOString().slice(0, 10) }
    }

    function showError(msg) {
      document.getElementById("syn-loading").style.display = "none"
      document.getElementById("syn-error").style.display = "block"
      document.getElementById("syn-error-msg").textContent = msg
    }

    function poll() {
      attempts++
      fetch(BACKEND + "/api/order/" + encodeURIComponent(orderId))
        .then(function(r) { return r.json() })
        .then(function(d) {
          if (d && d.status === "paid" && d.licenseKey) {
            // Order paid — render details.
            document.getElementById("syn-tier").textContent = (d.tier || "").toUpperCase()
            document.getElementById("syn-period").textContent = d.period || ""
            // We don't get expiresAt from /api/order; estimate from paidAt + period.
            const days = d.period === "annual" ? 366 : 31
            const expires = (d.paidAt || Date.now()) + days * 24 * 60 * 60 * 1000
            document.getElementById("syn-expires").textContent = fmtDate(expires)
            document.getElementById("syn-key").textContent = d.licenseKey
            document.getElementById("syn-loading").style.display = "none"
            document.getElementById("syn-details").style.display = "block"
            // Wire copy button.
            const copyBtn = document.getElementById("syn-copy")
            copyBtn.addEventListener("click", function() {
              navigator.clipboard.writeText(d.licenseKey).then(function() {
                copyBtn.textContent = "Copied!"
                setTimeout(function() { copyBtn.textContent = "Copy" }, 2000)
              })
            })
            return
          }
          // Not paid yet — keep polling.
          if (attempts < MAX_ATTEMPTS) {
            setTimeout(poll, 5000)
          } else {
            showError("Payment still pending after 2.5 minutes. Refresh this page, or check your wallet to confirm the USDT transfer completed.")
          }
        })
        .catch(function(e) {
          if (attempts < MAX_ATTEMPTS) {
            setTimeout(poll, 5000)
          } else {
            showError("Network error: " + (e.message || e))
          }
        })
    }

    poll()
  })()
</script>

---

## Need help?

Email <asam4r@gmail.com> with your order ID. We reply within 24 hours.
