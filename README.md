
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Talk & support</title>

<style>
body{
  margin:0;
  background:#0f1726;
  font-family:Arial, Helvetica, sans-serif;
  color:white;
}
.container{
  max-width:800px;
  margin:30px auto;
  background:#121a2a;
  padding:25px;
  border-radius:12px;
  border:1px solid #2a3a5c;
}
h1{margin-top:0}
label{display:block;margin-top:15px;font-size:14px}
input,select,textarea{
  width:100%;
  margin-top:5px;
  padding:10px;
  border-radius:8px;
  border:1px solid #2a3a5c;
  background:#0f1726;
  color:white;
}
textarea{resize:vertical;min-height:80px}
button{
  margin-top:20px;
  padding:12px;
  width:100%;
  border:none;
  border-radius:8px;
  background:#4b7bff;
  color:white;
  font-weight:bold;
  cursor:pointer;
}
#status{
  margin-top:15px;
  text-align:center;
  font-size:14px;
}
.disclaimer{
  font-size:13px;
  color:#b7c0d9;
  margin-bottom:15px;
}
</style>
</head>

<body>
<div class="container">
<h1>Talk & support</h1>

<div class="disclaimer">
<strong>Disclaimer:</strong> we respect your privacy. Information shared will be handled carefully.
If there is a serious safety concern we may encourage you to seek appropriate support.
</div>

<form id="supportForm">

<label>Discord username</label>
<input id="discordUsername" required>

<label>Discord ID</label>
<input id="discordId" required>

<label>What are you hoping for today?</label>
<select id="hope" required>
<option value="">Select</option>
<option>Just someone to listen</option>
<option>Advice</option>
<option>Reassurance</option>
<option>I’m not sure</option>
<option>Something else</option>
</select>

<label>How are you feeling right now?</label>
<textarea id="feeling" required></textarea>

<label>Is this about?</label>
<select id="topic" required>
<option value="">Select</option>
<option>School</option>
<option>Friends</option>
<option>Family</option>
<option>Relationships</option>
<option>Mental health</option>
<option>Something else</option>
</select>

<label>Is there anything you don’t want to be asked?</label>
<textarea id="dontAsk"></textarea>

<label>Are you safe?</label>
<select id="safe" required>
<option value="">Select</option>
<option>Yes</option>
<option>Not sure</option>
<option>No</option>
</select>

<label>How would you like the conversation to feel?</label>
<select id="feel" required>
<option value="">Select</option>
<option>Calm and gentle</option>
<option>Straightforward</option>
<option>Reassuring</option>
<option>Just listening</option>
</select>

<label>What time would you like the appointment to be?</label>
<input id="time" required>

<button type="submit">Send</button>
<div id="status"></div>

</form>
</div>

<script>
const WEBHOOK = "https://corsproxy.io/?https://discord.com/api/webhooks/1468878412272631870/rNIzLI8GM2BDSHh0hu3ucOgWSBGWVBNFM_ZV4Na5MVhk-bRzhvWj1I4lI1pgsplAFg7I";
const PING = "<@&1468878267154169948>";

document.getElementById("supportForm").addEventListener("submit", async function(e){
  e.preventDefault();
  const status = document.getElementById("status");
  status.innerText = "Sending...";

  const val = id => document.getElementById(id).value || "Not provided";

  const data = {
    username: "talkandsupport",
    content: PING,
    embeds: [{
      title: "New Talk & support request",
      color: 4886754,
      fields: [
        {name:"Discord username", value:val("discordUsername"), inline:true},
        {name:"Discord ID", value:val("discordId"), inline:true},
        {name:"Hoping for", value:val("hope")},
        {name:"Feeling", value:val("feeling")},
        {name:"Topic", value:val("topic"), inline:true},
        {name:"Do not ask", value:val("dontAsk")},
        {name:"Are they safe?", value:val("safe"), inline:true},
        {name:"Conversation style", value:val("feel"), inline:true},
        {name:"Requested time", value:val("time"), inline:true}
      ],
      timestamp: new Date().toISOString()
    }]
  };

  try{
    const res = await fetch(WEBHOOK,{
      method:"POST",
      headers:{"Content-Type":"application/json"},
      body:JSON.stringify(data)
    });

    if(!res.ok) throw new Error();

    status.innerText = "That’s all received, you will receive a Discord message From: talkandsupport";
    this.reset();

  }catch{
    status.innerText = "Failed to send. Please refresh and try again.";
  }
});
</script>

</body>
</html>
