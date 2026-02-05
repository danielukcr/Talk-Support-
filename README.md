
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Talk &amp; support</title>
  <style>
    :root { color-scheme: dark; }
    body { margin: 0; font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif; background:#0b0f17; color:#e6e9f2; }
    .wrap { max-width: 820px; margin: 32px auto; padding: 0 16px; }
    .card { background:#121a2a; border:1px solid #24304a; border-radius:16px; padding: 20px; box-shadow: 0 10px 30px rgba(0,0,0,.25); }
    h1 { margin: 0 0 12px; font-size: 22px; }
    .disclaimer { font-size: 13px; line-height: 1.4; color:#b7c0d9; background:#0f1726; border:1px solid #23314d; padding: 12px; border-radius: 12px; }
    form { margin-top: 16px; display:grid; gap: 14px; }
    label { display:block; font-size: 13px; color:#c9d2ea; margin-bottom: 6px; }
    input, select, textarea {
      width: 100%; box-sizing: border-box;
      background:#0f1726; color:#e6e9f2;
      border: 1px solid #2a3a5c; border-radius: 12px;
      padding: 10px 12px; outline: none;
    }
    textarea { min-height: 92px; resize: vertical; }
    input:focus, select:focus, textarea:focus { border-color:#4b7bff; box-shadow: 0 0 0 3px rgba(75,123,255,.2); }
    .row { display:grid; gap: 14px; grid-template-columns: 1fr 1fr; }
    @media (max-width: 720px) { .row { grid-template-columns: 1fr; } }
    .btns { display:flex; gap: 10px; align-items:center; }
    button {
      border:0; border-radius: 12px; padding: 10px 14px; cursor:pointer;
      background:#4b7bff; color:white; font-weight: 650;
    }
    button.secondary { background:#22304a; color:#e6e9f2; border:1px solid #2a3a5c; }
    .status { font-size: 13px; color:#b7c0d9; }
    .status.ok { color:#87f0b4; }
    .status.err { color:#ff9aa2; }
    .small { font-size: 12px; color:#97a3c1; }
  </style>
</head>

<body>
  <div class="wrap">
    <div class="card">
      <h1>Talk &amp; support</h1>

      <div class="disclaimer">
        <strong>Disclaimer:</strong> we respect your privacy. information shared will be handled carefully.
        if there is a serious safety concern we may encourage you to seek appropriate support.
      </div>

      <form id="supportForm" autocomplete="on">
        <div class="row">
          <div>
            <label for="discordUsername">Discord username:</label>
            <input id="discordUsername" name="discordUsername" type="text" placeholder="e.g. user#1234" required />
          </div>
          <div>
            <label for="discordId">Discord ID:</label>
            <input id="discordId" name="discordId" type="text" placeholder="e.g. 123456789012345678" required />
          </div>
        </div>

        <div>
          <label for="hope">What are you hoping for today?</label>
          <select id="hope" name="hope" required>
            <option value="" selected disabled>Select one…</option>
            <option>Just someone to listen</option>
            <option>Advice</option>
            <option>Reassurance</option>
            <option>I’m not sure</option>
            <option>Something else</option>
          </select>
          <div class="small">Tip: choose “Something else” and add details below if needed.</div>
        </div>

        <div>
          <label for="feeling">How are you feeling right now?</label>
          <textarea id="feeling" name="feeling" placeholder="Share as much or as little as you want…" required></textarea>
        </div>

        <div class="row">
          <div>
            <label for="topic">Is this about…?</label>
            <select id="topic" name="topic" required>
              <option value="" selected disabled>Select one…</option>
              <option>School</option>
              <option>Friends</option>
              <option>Family</option>
              <option>Relationships</option>
              <option>Mental health</option>
              <option>Something else</option>
            </select>
          </div>

          <div>
            <label for="safe">Are you safe?</label>
            <select id="safe" name="safe" required>
              <option value="" selected disabled>Select one…</option>
              <option>Yes</option>
              <option>Not sure</option>
              <option>No</option>
            </select>
          </div>
        </div>

        <div>
          <label for="dontAsk">Is there anything you don’t want to be asked?</label>
          <textarea id="dontAsk" name="dontAsk" placeholder="Optional — boundaries you want respected."></textarea>
        </div>

        <div>
          <label for="feel">How would you like the conversation to feel?</label>
          <select id="feel" name="feel" required>
            <option value="" selected disabled>Select one…</option>
            <option>Calm and gentle</option>
            <option>Straightforward</option>
            <option>Reassuring</option>
            <option>Just listening</option>
          </select>
        </div>

        <div>
          <label for="apptTime">What time would you like the appointment to be?</label>
          <input id="apptTime" name="apptTime" type="text" placeholder="e.g. 7pm Friday / 2026-02-06 19:00" required />
          <div class="small">If you want, you can include your timezone (e.g. “GMT”).</div>
        </div>

        <div class="btns">
          <button type="submit">Send</button>
          <button type="button" class="secondary" id="clearBtn">Clear</button>
          <span id="status" class="status"></span>
        </div>

        <div class="small">
          Note: This sends directly to a Discord webhook from the browser. For real deployments, consider moving the webhook call
          server-side so your webhook URL isn’t exposed.
        </div>
      </form>
    </div>
  </div>

  <script>
    <script>
  // --- Config ---
  const WEBHOOK_URL = "https://discord.com/api/webhooks/1468878412272631870/rNIzLI8GM2BDSHh0hu3ucOgWSBGWVBNFM_ZV4Na5MVhk-bRzhvWj1I4lI1pgsplAFg7I";
  const PING_ROLE = "<@&1468878267154169948>";

  const form = document.getElementById("supportForm");
  const statusEl = document.getElementById("status");
  const clearBtn = document.getElementById("clearBtn");

  function setStatus(msg, type) {
    statusEl.textContent = msg || "";
    statusEl.classList.remove("ok", "err");
    if (type) statusEl.classList.add(type);
  }

  function escapeMd(text) {
    return String(text || "")
      .replaceAll("\\", "\\\\")
      .replaceAll("`", "\\`")
      .replaceAll("*", "\\*")
      .replaceAll("_", "\\_")
      .replaceAll("~", "\\~")
      .replaceAll("|", "\\|")
      .replaceAll("<", "\\<")
      .replaceAll(">", "\\>");
  }

  clearBtn.addEventListener("click", () => {
    form.reset();
    setStatus("");
  });

  form.addEventListener("submit", async (e) => {
    e.preventDefault();
    setStatus("Sending…");

    const data = new FormData(form);

    const payload = {
      username: "talkandsupport",
      content: PING_ROLE,
      embeds: [
        {
          title: "New Talk & support request",
          color: 0x4b7bff,
          fields: [
            { name: "Discord username", value: escapeMd(data.get("discordUsername")), inlin

</body>
</html>
