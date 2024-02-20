---
layout: default
title: MCAMC Registration
permalink: /mcamc/register/
---
## MCAMC Registration

#### Registration costs $30 per individual and $90 per team of 3-4 students (regardless of team size). While recieving money from team members/students for payment keep in mind MySchoolBucks(payment platform) has a 4.95% service charge on the amount being paid.

### I am registering as a/an...
<div style="text-align: center">
<span class="reg-choice" id="reg0" onclick="reg(0)"> ... individual. </span>
<span class="reg-choice" id="reg1" onclick="reg(1)"> ... team of 3 or 4 students. </span>
</div>
<div class="cognito">
<script src="https://www.cognitoforms.com/s/5RmzrxaElkSFbjwAX0LpWA"></script>
</div>
<script type="text/javascript">
var choiceMade = false;
function reg(type)
{
  document.getElementById("reg0").style.display = "none";
  document.getElementById("reg1").style.display = "none";
  document.getElementById("i-am-registering-as-aan").style.display = "none";
  document.getElementById("mcamc-registration").style.display = "none";
  if (!choiceMade) {
    if (type === 0) {
      Cognito.load("forms", { id: "14" });
      Cognito.resize();
    }
    if (type === 1) {
      Cognito.load("forms", { id: "13" });
      Cognito.resize();
    }
    choiceMade = true;
  }
}
</script>
