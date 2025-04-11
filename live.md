---
layout: default
title: MCAMC Live Round
permalink: /mcamc/live/
meta: | 
  <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
  <script type="text/x-mathjax-config">
    MathJax.Hub.Config({
      tex2jax: {
        skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
        inlineMath: [['$','$']]
      }
    });
  </script>
  <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
---
<div id="content"></div>
<button id="authorize_button" style="display: inline;">Authorize</button>
<button id="signout_button" style="display: inline;">Sign Out</button>
<div class="cwrapper">
<div id="live-table" class="mcamc-table" style="float: left"></div>
<div style="width: 2px"></div>
<div id="live-table2" class="mcamc-table" style="float: left"></div>
<div style="width: 2px"></div>
<div id="live-table3" class="mcamc-table" style="float: left"></div>
<script type="text/javascript">
  var CLIENT_ID = '570864691277-5mrhsfgqd0in8tqn7jvfin3ocn9furga.apps.googleusercontent.com';
  var API_KEY = 'AIzaSyCTlqgObl4U_BXEk3aJxi_R42tRH9128nw';
  var SCOPES = "https://www.googleapis.com/auth/spreadsheets.readonly";
  var authorizeButton = document.getElementById('authorize_button');
  var signoutButton = document.getElementById('signout_button');
  let tokenClient;
  let gapiInited;
  let gisInited;
  authorizeButton.style.visibility="hidden";
  signoutButton.style.visibility="hidden";
  authorizeButton.onclick = handleAuthClick;
  signoutButton.onclick = handleSignoutClick;
  function checkBeforeStart()
  {
    if (gapiInited && gisInited)
    {
      authorizeButton.style.visibility="visible";
      signoutButton.style.visibility="visible";
    }
  }
  function gapiInit()
  {
    gapi.client.init({}).then(function()
    {
      gapi.client.load('https://sheets.googleapis.com/$discovery/rest?version=v4');
      gapiInited = true;
      checkBeforeStart();
    });
  }
  function gapiLoad()
  {
      gapi.load('client', gapiInit)
  }
  function gisInit()
  {
    tokenClient = google.accounts.oauth2.initTokenClient({
      client_id: CLIENT_ID,
      scope: SCOPES,
      callback: '',
    });
    gisInited = true;
    checkBeforeStart();
  }
  function handleAuthClick(event)
  {
    tokenClient.callback = (resp) =>
    {
      if (resp.error !== undefined)
      {
        throw(resp);
      }
      authorizeButton.style.visibility="hidden";
      signoutButton.style.visibility="hidden";
      listMajors();
    }
    if (gapi.client.getToken() === null)
    {
      tokenClient.requestAccessToken({prompt: 'consent'});
    }
    else
    {
      tokenClient.requestAccessToken({prompt: ''});
    }
  }
  function handleSignoutClick(event)
  {
    let cred = gapi.client.getToken();
    if (cred !== null)
    {
      google.accounts.oauth2.revoke(cred.access_token, () => {console.log('Revoked: ' + cred.access_token)});
      gapi.client.setToken('');
    }
  }
  function appendPre(message)
  {
    var pre = document.getElementById('content');
    var textContent = document.createTextNode(message + '\n');
    pre.appendChild(textContent);
  }
  function analyzeRow(row)
  {
    var rowData = {};
    sum = row.slice(2, row.length).reduce((a, b) => parseInt(a) + parseInt(b));
    rowData.score = sum;
    rowData.setsComplete = Math.floor(row.slice(2, row.length).filter(function(val) { return parseInt(val) !== 0; }).length/3);
    rowData.teamName = row[1];
    rowData.teamNumber = row[0];
    return rowData
  }
  var scores = [];
  function onOpenFunc() {
    PropertiesService.getScriptProperties().setProperty("accessToken", ScriptApp.getOAuthToken());
  }
  function listMajors()
  {
    gapi.client.sheets.spreadsheets.values.get({
      spreadsheetId: '1OuOhf3g0ew-fBaEvwO5Qgr3sE9E4y_BAI7_xfhBK7X4',
      range: 'Data!2:29',
    }).then(function(response)
    {
      var range = response.result;
      if (range.values.length > 0) {
        for (i = 0; i < range.values.length; i++) {
          var row = range.values[i];
          rowData = analyzeRow(row);
          scores[i] = [];
          scores[i][0] = rowData.teamNumber;
          scores[i][1] = rowData.teamName;
          scores[i][2] = rowData.score;
          scores[i][3] = rowData.setsComplete;
        }
      }
    }, function(response) {});
    scores.sort(function(a,b) { return b[2] - a[2]});
    var html = "<table><tbody><tr><td>#</td><td>Name         </td><td>Score</td><td>Sets</td></tr>";
    var split = Math.round((scores.length/3));
    for (var i = 0; i < split; i++)
    {
      html+="<tr>";
      html+="<td>"+scores[i][0]+"</td>";
      html+="<td>"+scores[i][1]+""+"</td>";
      html+="<td style=\"text-align:right\">"+scores[i][2]+"</td>";
      html+="<td>"+scores[i][3]+"/8"+"</td>";
      html+="</tr>";
    }
    html+="</tbody></table>";
    var html2 = "<table><tbody><tr><td>#</td><td>Name         </td><td>Score</td><td>Sets</td></tr>";
    for (var i = split; i < (split*2); i++)
    {
      html2+="<tr>";
      html2+="<td style=\"border-left: solid 1px black\">"+scores[i][0]+"</td>";
      html2+="<td>"+scores[i][1]+""+"</td>";
      html2+="<td style=\"text-align:right\">"+scores[i][2]+"</td>";
      html2+="<td>"+scores[i][3]+"/8"+"</td>";
      html2+="</tr>";
    }
    html2+="</tbody></table>";
    var html3 = "<table><tbody><tr><td>#</td><td>Name         </td><td>Score</td><td>Sets</td></tr>";
    for (var i = split*2; i < scores.length; i++)
    {
      html3+="<tr>";
      html3+="<td style=\"border-left: solid 1px black\">"+scores[i][0]+"</td>";
      html3+="<td>"+scores[i][1]+""+"</td>";
      html3+="<td style=\"text-align:right\">"+scores[i][2]+"</td>";
      html3+="<td>"+scores[i][3]+"/8"+"</td>";
      html3+="</tr>";
    }
    html3+="</tbody></table>";
    document.getElementById("live-table").innerHTML = html;
    document.getElementById("live-table2").innerHTML = html2;
    document.getElementById("live-table3").innerHTML = html3;
    setTimeout(listMajors, 5000);
  }
</script>
</div>
<script async defer src="https://apis.google.com/js/api.js" onload="gapiLoad()"></script>
<script async defer src="https://accounts.google.com/gsi/client" onload="gisInit()"></script>
