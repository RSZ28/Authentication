<html lang="en">
    <head>
        <meta charset="UTF-8">
        <style>
            body{
                font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
                font-size: 11pt;
                user-select:  text;
            }

            footer, a{
                user-select: none;
            }

            textarea{
                font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
                font-size: 11pt;
            }

            .reloj{
                font-size: 15px;
                font-family: 'Segoe UI', sans-serif;
                margin: 11px 0;
            }

            .tab-buttons {
            display: flex;
            margin-bottom: 10px;
            }

            .subtab-buttons {
            display: flex;
            margin-bottom: 10px;
            }

            .generated-text{                
                font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
                font-size: 11pt;
                user-select: text;
                border: 1px solid #ccc;
                padding: 10px;
                cursor: text;
            }

            .generated-link a {
                user-select: text; /* Asegura que los enlaces también puedan seleccionarse */
                color: blue;
                text-decoration: underline;            
            }

            .generated-link a:focus {
                outline: none;
            }

            .tab-buttons button {
            padding: 10px 20px;
            border: 1px solid #ccc;
            background-color: #eee;
            cursor: pointer;
            margin-right: 5px;
            }

            .subtab-buttons button {
            padding: 10px 20px;
            border: 1px solid #ccc;
            background-color: #eee;
            cursor: pointer;
            margin-right: 5px;
            }

            .tab-buttons button.active {
            background-color: #ddd;
            font-weight: bold;
            }

            .subtab-buttons button.active {
            background-color: #ddd;
            font-weight: bold;
            }

            .tab-content {
            display: none;
            border: 1px solid #ccc;
            padding: 15px;
            }

            .subtab-content {
            display: none;            
            padding: 15px;
            }

            .tab-content.active {
            display: block;
            }

            .subtab-content.active {
            display: block;
            }

            .texto-copiable {
                border: 1px solid #ccc;
                padding: 10px;
                width: fit-content;
                user-select: text; /* permite seleccionar el texto */
            }

        </style>
        <link rel="stylesheet" href="style.css">
        <script>
            let createdHD = [];
            var templatesContent = {};
            const dbURL = "https://templatesapi-awgkb6azg4a8dcdm.centralus-01.azurewebsites.net/auth";
            async function GetTemplates(tempID){
                //const url = 'https://raw.githubusercontent.com/RSZ28/Templates_Content/refs/heads/main/Templates_Content.json';

                try{
                    const rsp = await fetch(dbURL);
                    if(!rsp.ok) throw new Error('Download Error');

                    const txt = await rsp.json();
                    var template = "";

                    txt.forEach(i => {
                        if(i.title === tempID) template = i.content;
                    });

                    return template;

                } catch(error){
                    console.error('Error: ',error);
                }
            }

            function boldString(str , substr){
                strRegExp = new RegExp(substr, 'g');
                return str.replace(strRegExp, '<b>'+substr+'</b>');
            }

            function getLastContact(){
                today = new Date();                
                if(today.getDay() === 1 || today.getDay() === 2) today.setDate(today.getDate()-4);
                else today.setDate(today.getDate()-2);
                
                return today;
            }

            function findHD(hdID, list){
                found = false;
                list.forEach(i =>{
                    if(hdID === i) return true;
                });
                return found;
            }

            async function getHolyDays(){

                try{
                    const rsp = await fetch(dbURL);
                    if(!rsp.ok) throw new Error('Download Error');

                    const txt = await rsp.json();
                    var template = "";

                    const HDCont = document.getElementById("HDContainer");
                    HDs = [];
                   
                    txt.forEach(i => {
                        if(i.title.includes("FU_HD")){
                            HDs.push(i);
                        }
                    });

                    if(createdHD.length !== 0){
                        dlt = [];
                        createdHD.forEach(i => {
                            if(!HDs.find(h => i === h.title)) dlt.push(i);
                        });
                        dlt.forEach(i => {
                            document.getElementById("CNT_"+i).remove();
                            createdHD.splice(i);
                        });
                        if(createdHD.length === 0){
                            //document.getElementById("ospButton").classList.add('active');
                            //document.getElementById("fHD").classList.remove('active');
                            //document.getElementById("f2").classList.remove('active');
                            //document.getElementById("osp").classList.add('active');
                        }
                    }

                    if(HDs.length > 0){
                        HDs.forEach(f =>{
                            if(createdHD.length !== 0){
                                if(!createdHD.find(i => i === f.title)){
                                    cnt = document.createElement("div");
                                    cnt.id = "CNT_"+f.title;
                                    rd = document.createElement("input");
                                    rd.type = "radio";
                                    rd.name = "FU_HD";
                                    rd.value = f.title;
                                    cnt.appendChild(rd);
                                    lbl = document.createElement("label");
                                    lbl.for = f.title;
                                    lbl.innerHTML = f.displayName;
                                    cnt.appendChild(lbl);
                                    HDCont.appendChild(cnt);
                                    createdHD.push(f.title);
                                }
                            }
                            else{
                                cnt = document.createElement("div");
                                cnt.id = "CNT_"+f.title;
                                rd = document.createElement("input");
                                rd.type = "radio";
                                rd.name = "FU_HD";
                                rd.value = f.title;
                                cnt.appendChild(rd);
                                lbl = document.createElement("label");
                                lbl.for = f.title;
                                lbl.innerHTML = f.displayName;
                                cnt.appendChild(lbl);
                                HDCont.appendChild(cnt);
                                createdHD.push(f.title);
                            }
                            
                        });
                    }

                } catch(error){
                    console.error('Error: ',error);
                }
            }

            function checkAvailableHD(){
                if(document.getElementById("HDContainer").childElementCount > 0) document.getElementById("fHD").style.display = "block";
                else document.getElementById("fHD").style.display = "none";
            }

            document.addEventListener("DOMContentLoaded", function() {
                //GetTemplates();
                SetName();
                function actualizarRelojes() {
                let date = new Date();                
                OSPDate.value = getLastContact().toISOString().split('T')[0];
                let ahora;
                const zonas = [
                    { id: 'pst', zona: 'America/Los_Angeles', nombre: 'Pacific Standard Time (PST)' },
                    { id: 'ast', zona: 'America/Puerto_Rico', nombre: 'Atlantic Standard Time (AST)' },
                    { id: 'mst', zona: 'America/Denver', nombre: 'Mountain Standard Time (MST)' },
                    { id: 'cst', zona: 'America/Chicago', nombre: 'Central Standar Time (CST)' },
                    { id: 'est', zona: 'America/New_York', nombre: 'Easter Standard Time (EST)' },
                    { id: 'nic', zona: 'America/Managua', nombre: 'Hora Nicaragua'},
                    { id: 'chl', zona: 'America/Santiago', nombre:'Hora de Chile'},
                    { id: 'col', zona: 'America/Bogota', nombre:'Hora de Colombia'},
                ];

                zonas.forEach(({ id, zona, nombre }) => {
                    ahora = date.toLocaleTimeString('en-US', {
                    timeZone: zona,
                    hour: '2-digit',
                    minute: '2-digit',
                    hour12: true
                    });
                    document.getElementById(id).textContent = `${nombre}: ${ahora}`;
                });                
            }
            setInterval(getHolyDays, 3000);
            //setInterval(checkAvailableHD, 3000);
            Look4Cases();
            setInterval(actualizarRelojes, 1000);
            actualizarRelojes();
            });
        </script>
    </head>    
    <body>
        <fieldset style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px;">
            <h1>Authentication QA Templates</h1>  
            <button onclick="window.open('https://forms.office.com/r/jXWnsVvRQQ','_blank','noopener,noreferrer')">Send Feedback</button>
        </fieldset>              
        <fieldset style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px;">
            <legend><b>Time Zones</b></legend>
            <fieldset>
                <div class="reloj" id="nic"></div>
                <div class="reloj" id="chl"></div>
                <div class="reloj" id="col"></div>
                <div class="reloj" id="ast"></div>
            </fieldset>
            <fieldset>
                <div class="reloj" id="pst"></div>
                <div class="reloj" id="cst"></div>
                <div class="reloj" id="est"></div>
                <div class="reloj" id="mst"></div> 
            </fieldset>
        </fieldset>
        <fieldset style="display: none;">
            <legend><b>Engineer Alias</b></legend>
            <div>
                <legend>Alias: <input id="eAlias" placeholder="Here goes your Alias" onchange="SetDate()"></legend>
            </div>
        </fieldset>
        <!-- <fieldset style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px;"> -->
        <fieldset>
            <fieldset>
                <legend style="display: none;"><b>Case Information</b></legend>
                <div style="display: none;">
                    <b style="color: #ff000f;">IT'S SEV A?</b>
                    <input type="checkbox" id="sevA" onchange="SetSEVAFollowUp()">
                </div>

                <div>
                    Do you know the client's name?
                    <input type="checkbox" id="nameRes" checked="True" onchange="SetName()">
                    <script>
                        function SetSEVAFollowUp(){
                            const subButtons = document.querySelectorAll('.subtab-btn');
                            const subContents = document.querySelectorAll('.subtab-content');
                            const buttons = document.querySelectorAll('.tab-btn');
                            const contents = document.querySelectorAll('.tab-content');
                            const sevA = document.getElementById("sevA").checked;
                            
                            subButtons.forEach(button => {
                                button.classList.remove('active');
                            });
                            subContents.forEach(cnt => {
                                cnt.classList.remove('active');
                            });
                            buttons.forEach(bnt => {
                                bnt.classList.remove('active');
                            });
                            contents.forEach(cnt => {
                                cnt.classList.remove('active');
                            });
                            
                            if(sevA === true){
                                document.getElementById("PCM").style.display = "none";
                                document.getElementById("tab4BNT").classList.add('active');
                                document.getElementById("tab4").classList.add('active');
                                document.getElementById("fA").classList.add('active');
                                document.getElementById("fAbutton").classList.add('active');
                            }
                            else{
                                document.getElementById("PCM").style.display = "block";
                                document.getElementById("tab2BNT").classList.add('active');
                                document.getElementById("tab2").classList.add('active');
                                document.getElementById("f1").classList.add('active');
                                document.getElementById("fButton").classList.add('active');
                            }
                        }

                        function SetName(){
                            const check = document.getElementById("nameRes");
                            const field = document.getElementById("clientNameF");
                            field.value = check.checked ? "" : "Admin";
                            field.readOnly = !check.checked ? true : false;
                        }
                    </script>
                </div>
                <div>
                    Client's First name:
                    <input type="text" id="clientNameF" placeholder="Here goes the client's name">
                </div>
                <div>
                    Case number: 
                    <style>
                        input[type=number]::-webkit-inner-spin-button,
                        input[type=number]::-webkit-outer-spin-button{
                            -webkit-appearance: none;
                            margin: 0;
                        }

                        input[type=number]{
                            appearance: textfield;
                        }
                    </style>
                    <input type="number" id="caseNumF" placeholder="Here goes the case number">
                </div>
                <div style="display: none;">
                    Client's Email:
                    <input type="email" id="clientEmailF" placeholder="Here goes the client's email">
                </div>
                <div style="display: none;">
                    Client's Phone Number:
                    <input type="text" id="clientPhoneF" placeholder="Here goes the client's phone">
                </div>
                <legend>Case Languange:</legend>
                <div>
                    <input type="radio" id="eng" name="language" value="eng" checked="True">
                    <label for="eng">English</label>
                    <input type="radio" id="esp" name="language" value="esp">
                    <label for="esp">Spanish</label>
                </div>
                <div id="PCM">
                    <legend>Case PCM:</legend>
                    <div>
                        <input type="radio" id="email" name="pcm" value="email" checked="true" onclick="ShowPHTypes()">
                        <label for="email">Email</label>
                        <input type="radio" id="phone" name="pcm" value="phone" onclick="ShowPHTypes()">
                        <label for="phone">Phone</label>
                    </div>
                </div>
                <button onclick="ClearFields()">Clear Fields</button>
                <script>
                    const slaLanguage = document.querySelectorAll('input[name="language"]');
                    const cKName = document.getElementById("nameRes");
                    const cName = document.getElementById("clientNameF");
                    const cNumber = document.getElementById("caseNumF");
                    const cEmail = document.getElementById("clientEmailF");
                    const cPhone = document.getElementById("clientPhoneF");

                    function ClearFields(){
                        cNumber.value = "";
                        cEmail.value = "";
                        cPhone.value = "";
                        if(cKName.checked === true) {
                            cName.value = "";
                        }
                        //slaLanguage.forEach(r => r.checked = false);
                    }
                </script>
            </fieldset>
            <fieldset style="display: none;">
                <legend><b>Stored Cases</b></legend>
                <div id="storedC">

                </div>
                <script>
                    function ClearButtonsPane(){
                        const casesPane = document.getElementById("storedC");

                        casesPane.childNodes.forEach(function (c) {
                            casesPane.removeChild(c)
                        });

                    }

                    function Look4Cases(){
                        const allKeys = Object.keys(localStorage);
                        const casesPane = document.getElementById("storedC");

                        ClearButtonsPane();

                        if(allKeys.length > 0){
                            for(let i=0; i<allKeys.length;i++){
                                let C = JSON.parse(localStorage.getItem(allKeys[i]));
                                let btn = document.createElement("button");
                                btn.name = C.cNumber;
                                btn.textContent = "Case: "+C.cNumber;
                                
                                btn.onclick = function () {GetCase(btn);};
                                casesPane.appendChild(btn);
                            }
                        }
                    }
                </script>
            </fieldset>
        </fieldset>

        <div class="tab-buttons">
            <button class="tab-btn" data-tab="tab1" style="display: none;">Master Note</button>
            <button class="tab-btn active" id="tab2BNT" data-tab="tab2">SLA</button>
            <button class="tab-btn" id="tab4BNT" data-tab="tab4">Follow Up</button>
            <button class="tab-btn" data-tab="tab5">Closure</button>
        </div>

        <!--SLA-->
        <div id="tab2" class="tab-content active">

            <div class="subtab-buttons">
                <button class="subtab-btn" data-tab="eN" onclick="Stabilization(true)">Stabilization</button>
                <button class="subtab-btn active" data-tab="paraf" onclick="Stabilization(false)">Regular SLA</button>
            </div>            

            <script>
                function ShowPHTypes(){
                    ShowPendingCX();
                    const slaType = document.querySelector('input[name="pcm"]:checked').value;
                    document.getElementById("eN").style.display = "none";
                    if(slaType === "email"){
                        document.getElementById("PH").style.display = "none";
                    }
                    if(slaType === "phone"){
                        document.getElementById("PH").style.display = "block";
                    }
                }
            </script>

            <fieldset id="PH" style="display: none;">
                <legend>SLA Type</legend>
                <div>
                    <input type="radio" name="phType" id="ph0" value="ph0" checked="true">
                    <label for="ph0">Answered Call</label>
                    <input type="radio" name="phType" id="ph1" value="ph1">
                    <label for="ph1">Long Call</label>
                    <input type="radio" name="phType" id="ph2" value="ph2">
                    <label for="ph2">Call not responded</label>
                </div>
            </fieldset>
            
            <fieldset class="subtab-content" id="eN">
                <legend>Engineer name:</legend>
                <input type="text" id="eName" placeholder="Please enter your name and last name">
            </fieldset>
            
            <fieldset class="subtab-content active" id="paraf">
                <legend>Case Parafrasing:</legend>
                <input type="text" id="cDes" placeholder="Here goes a brief parafrasing" style="height: 25px; width: 400px;">
            </fieldset>
            <div>
                <button id="premSLA" onclick="GenerateSLA()">Generate SLA</button>
            </div>           

            <script>
                let stab = false;
                function Stabilization(val){
                    stab = val;
                    console.log(stab);
                }

                async function GenerateSLA(){
                    const slaType = document.querySelector('input[name="pcm"]:checked').value;
                    const phType = document.querySelector('input[name="phType"]:checked').value;
                    const slaLanguage = document.querySelector('input[name="language"]:checked').value;
                    const eName = document.getElementById("eName").value;
                    const cKName = document.getElementById("nameRes").value;
                    const cName = document.getElementById("clientNameF").value;
                    const cNumber = document.getElementById("caseNumF").value;
                    const cEmail = document.getElementById("clientEmailF").value;
                    const cPhone = document.getElementById("clientPhoneF").value;
                    const cDesc = document.getElementById("cDes").value;
                    let template = "";

                    if(slaLanguage === "eng"){
                        if(stab){
                            template = await GetTemplates("SLA_STL_ENG");
                        }
                        else{
                            if(slaType === "email") template = await GetTemplates("SLA_EM_ENG");
                            else if(slaType === "phone"){
                                if(phType === "ph0") template = await GetTemplates("SLA_ANW_ENG");
                                else if(phType === "ph1") template = await GetTemplates("SLA_LC_ENG");
                                else if(phType === "ph2") template = await GetTemplates("SLA_NANW_ENG");
                            }
                        }
                    }
                    else if(slaLanguage === "esp"){
                        if(stab){
                            template = await GetTemplates("SLA_STL_ESP");
                        }
                        else{
                            if(slaType === "email") template = await GetTemplates("SLA_EM_ESP");
                            else if(slaType === "phone"){
                                if(phType === "ph0") template = await GetTemplates("SLA_ANW_PH_ESP");
                                else if(phType === "ph1") template = await GetTemplates("SLA_LC_PH_ESP");
                                else if(phType === "ph2") template = await GetTemplates("SLA_NANW_PH_ESP");
                            }
                        }
                    }

                    console.log("PCM "+slaType+"\nLanguage "+slaLanguage+"\nPHtype "+phType);
                    if(template.includes("NAME")) template = template.replace("NAME",cName);
                    if(template.includes("ENAME")) template = template.replace("ENAME", eName);
                    if(template.includes("EM")) template = template.replace("EM", "<b>"+cEmail+"</b>");
                    if(template.includes("PH")) template = template = template.replace("PH","<b>" + cPhone +"</b>");
                    if(template.includes("TICKET")) template = template.replace("TICKET","<b>" + cNumber + "</b>");
                    if(template.includes("DESC")) template = template.replace("DESC", "<b>" + cDesc + "</b>");

                    document.getElementById("premResult").innerHTML = template;//.replace(/\n/g,"<br>");
                }
            </script>

            <fieldset>
                <legend>SLA Generation</legend>
                <div class="generated-text" id="premResult" onpaste="return false" contenteditable="true"></div>
                <script>
                    let SLActrl = false;
                    let SLAe = false;

                    document.getElementById("premResult").addEventListener("keyup",function(event){
                        if(event.ctrlKey){
                            ctrl=true;
                        }
                        if(event.key === 'c' || event.key === 'C'){
                            event.preventDefault();
                            e = true;
                        }
                    });

                    document.getElementById("premResult").addEventListener("keydown", function(event) {
                        //const selectedText = window.getSelection().toString();
                        if(event.ctrlKey){
                            ctrl=true;
                        }
                        else{
                            event.preventDefault();
                        }
                        if(event.key === 'c' || event.key === 'C'){                            
                            e = true;
                        }
                        else{
                            event.preventDefault()
                        }
                    });
                </script>
            </fieldset>

        </div>

        <!--Follow Up-->
        <div id="tab4" class="tab-content">
            <div class="subtab-buttons">
                <button class="subtab-btn" id="fAbutton" data-tab="fA" style="display: none;">SEV A</button>
                <button class="subtab-btn" id="fButton" data-tab="f1" style="display: block;">Strike</button>
                <button class="subtab-btn active" id="ospButton" data-tab="osp">OSP</button>
                <button class="subtab-btn" id="stabButton" onclick="GenerateFollowUp(true)">Stabilization</button>
                <button class="subtab-btn" id="fHD" style="display: none;" data-tab="f2">Holydays</button>
            </div>

            <script>
                function ShowPendingCX(){
                    const slaType = document.querySelector('input[name="pcm"]:checked').value;
                    document.getElementById("eN").style.display = "none";
                    if(slaType === "email"){
                        document.getElementById("pendingInfo").style.display = "block";
                        document.getElementById("phLCA").style.display = "none";
                    }
                    if(slaType === "phone"){
                        document.getElementById("pendingInfo").style.display = "none";
                        document.getElementById("phLCA").style.display = "block";
                    }
                }

                function ShowLCADate(){
                    let wrn = document.getElementById("OSPEnd").checked;
                    //console.warn(wrn);
                    if(wrn) document.getElementById("dateCont").style.display = "block";
                    else document.getElementById("dateCont").style.display = "none";
                }
            </script>

            <fieldset id="fA" class="subtab-content">
                <legend>SEV A Unresponsive Process</legend>
                <div>
                    <legend>Follow Up Number:</legend>
                    <input type="radio" id="stA" name="sAnum" value="1" checked>
                    <label for="stA">1</label>
                    <input type="radio" id="ndA" name="sAnum" value="2">
                    <label for="ndA">2</label>
                    <input type="radio" id="thA" name="sAnum" value="3">
                    <label for="thA">3</label>
                </div>
                <div><legend>Time of the Call UTC-6: <input type="time" id="callTimeA"></legend></div>
                <div style="padding-top: 10px;"><legend>Case Description: <input type="text" id="followADes" placeholder="Here goes the case description"></legend></div>
                <div style="padding-top: 10px;"><button onclick="GenerateSEVAFollowUp()">Generate</button></div>
            </fieldset>
                
            <fieldset id="f1" class="subtab-content">
                <legend>Unresponsive Process</legend>
                <div>
                    <legend>Follow Up Number:</legend>
                    <input type="radio" id="st" name="sNum" value="1" checked>
                    <label for="st">1</label>
                    <input type="radio" id="nd" name="sNum" value="2">
                    <label for="nd">2</label>
                    <input type="radio" id="th" name="sNum" value="3">
                    <label for="th">3</label>
                </div>
                <div><legend>Time of the Call UTC-6: <input type="time" id="callTime"></legend></div>
                <div style="padding-top: 10px;"><legend>Case Description: <input type="text" id="followDes" placeholder="Here goes the case description"></legend></div>
                <div style="padding-top: 10px;"><button onclick="GenerateFollowUp()">Generate</button></div>
            </fieldset>

            <fieldset id="f2" class="subtab-content">
                <legend>Holidays Follow Ups</legend>
                <div id="HDContainer"></div>
                <div style="padding-top: 15px;">
                    <button onclick="GenerateHolydays()">Generate</button>
                </div>
            </fieldset>

            <fieldset id="osp" class="subtab-content active">
                <fieldset id="pendingInfo" style="display: block;">
                    <legend>Pending from customer</legend>
                    <input type="radio" id="pi" name="pnd" value="pi" checked>
                    <label for="pi">Pending Info</label>
                    <input type="radio" id="nd" name="pnd" value="pc">
                    <label for="pc">Pending Confirmation</label>
                </fieldset>
                
                <fieldset>
                    <legend>Follow Up Info</legend>
                    <div id="OSPWarn">
                        <legend>Closure Warning?: <input type="checkbox" id="OSPEnd" onclick="ShowLCADate()"></legend>
                        <div id="dateCont" style="display: none;"><legend style="padding-top: 10px;">Last contact attempt: <input type="date" id="OSPDate"></legend></div>
                    </div>
                    <div id="emLCA" style="display: block;">
                        <div style="padding-top: 10px;"><legend>Case Description: <input type="text" id="OSPDesc" placeholder="Here goes the case description"></legend></div>
                    </div>
                    
                    <div id="phLCA" style="display: none; padding-top: 10px;">
                        <div><legend>Time of the Call UTC-6: <input type="time" id="OSPcallTime"></legend></div>
                        <div style="padding-top: 10px;"><legend>Call Reason: <input type="text" id="callReason"></legend></div>
                    </div>               
                    <div style="padding-top: 10px;"><button onclick="GenerateOSP()">Generate</button></div>
                </fieldset>
                
            </fieldset>

            <fieldset>
                <legend>Template Result</legend>
                <div id="followUp"></div>
            </fieldset>
            <script>
                
                async function GenerateOSP(){
                    const cName = document.getElementById("clientNameF").value;
                    const cNumber = document.getElementById("caseNumF").value;
                    const cDes = document.getElementById("OSPDesc").value;
                    const callR = document.getElementById("callReason").value;
                    const timeReference = document.getElementById("OSPcallTime").value;
                    const OSPclosure = document.getElementById("OSPEnd").checked;
                    const slaType = document.querySelector('input[name="pcm"]:checked').value;
                    const slaLanguage = document.querySelector('input[name="language"]:checked').value;
                    const pendingCX = document.querySelector('input[name="pnd"]:checked').value;
                    const date = getLastContact();
                    day = String(date.getDate()).padStart(2,'0');
                    month = String(date.getMonth() + 1).padStart(2,'0');
                    template = "";

                    if(slaLanguage === "eng"){
                        if(OSPclosure) template = await GetTemplates("CL_ST_ENG");
                        else{
                            if(slaType ==="email"){
                                if(pendingCX === "pi") template = await GetTemplates("OS_PI_EM_ENG");
                                else if(pendingCX === "pc") template = await GetTemplates("OS_PC_EM_ENG");
                            }
                            else if(slaType === "phone") template = await GetTemplates("OS_FU_PH_ENG")
                        }
                    }
                    else if(slaLanguage === "esp"){
                        if(OSPclosure) template = await GetTemplates("CL_ST_ESP");
                        else{
                            if(slaType ==="email"){
                                if(pendingCX === "pi") template = await GetTemplates("OS_PI_EM_ESP");
                                else if(pendingCX === "pc") template = await GetTemplates("OS_PC_EM_ESP");
                            }
                            else if(slaType === "phone") template = await GetTemplates("OS_FU_PH_ESP");
                        }
                    }

                    if(template.includes("NAME")) template = template.replace("NAME",cName);
                    if(template.includes("TIME")) template = template.replace("TIME","<b>" + callTime +" UTC-6</b>");
                    if(template.includes("DATE")) template = template.replace("DATE","<b>" + `${day}/${month}` +"</b>");
                    if(template.includes("REASON")) template = template.replace("REASON","<b>" + callR +"</b>");
                    //if(template.includes("PH")) template = template = template.replace("PH","<b>" + cPhone +"</b>");
                    if(template.includes("TICKET")) template = template.replace("TICKET","<b>" + cNumber + "</b>");
                    if(template.includes("DESC")) template = template.replace("DESC", "<b>" + cDes + "</b>");


                    document.getElementById("followUp").innerHTML = template;//.replace(/\n/g,"<br>");

                }

                async function GenerateSEVAFollowUp(){
                    const cName = document.getElementById("clientNameF").value;
                    const cNumber = document.getElementById("caseNumF").value;
                    const cPhone = document.getElementById("clientPhoneF").value;
                    const cDes = document.getElementById("followADes").value;
                    const timeReference = document.getElementById("callTimeA").value;
                    const slaLanguage = document.querySelector('input[name="language"]:checked').value;
                    const strikeNum = document.querySelector('input[name="sAnum"]:checked').value;

                    //#region 12 Hours format conversor
                    let [h, min] = timeReference.split(':');
                    let hours = parseInt(h,10);
                    let dayzone = '';
                    if(hours === 0){
                        hours = 12;
                        dayzone = 'am';
                    }
                    else if(hours === 12){
                        dayzone = 'pm';
                    }
                    else if(hours > 12){
                        hours -= 12;
                        dayzone = 'pm';
                    }
                    else{
                        dayzone = 'am';
                    }
                    let callTime = hours+":"+min+dayzone;
                    //#endregion

                    if(slaLanguage === "eng"){
                        if(strikeNum === "1") template = await GetTemplates("FU_SEVA_S1_ENG");
                        else if(strikeNum === "2") template = await GetTemplates("FU_SEVA_S2_ENG");
                        else if(strikeNum === "3") template = await GetTemplates("FU_SEVA_S3_ENG");
                    }
                    else if(slaLanguage === "esp"){
                        if(strikeNum === "1") template = await GetTemplates("FU_SEVA_S1_ESP");
                        else if(strikeNum === "2") template = await GetTemplates("FU_SEVA_S2_ESP");
                        else if(strikeNum === "3") template = await GetTemplates("FU_SEVA_S3_ESP");
                    }

                    if(template.includes("NAME")) template = template.replace("NAME",cName);
                    if(template.includes("TIME")) template = template.replace("TIME","<b>" + callTime +" UTC-6</b>");
                    if(template.includes("PH")) template = template = template.replace("PH","<b>" + cPhone +"</b>");
                    if(template.includes("TICKET")) template = template.replace("TICKET","<b>" + cNumber + "</b>");
                    if(template.includes("DESC")) template = template.replace("DESC", "<b>" + cDes + "</b>");


                    document.getElementById("followUp").innerHTML = template;//.replace(/\n/g,"<br>");

                }

                async function GenerateHolydays(){
                    const hd = document.querySelector('input[name=FU_HD]:checked').value;
                    const cName = document.getElementById("clientNameF").value;
                    const cNumber = document.getElementById("caseNumF").value;

                    template = await GetTemplates(hd);

                    if(template.includes("NAME")) template = template.replace("NAME",cName);
                    //if(template.includes("CALL")) template = template.replace("CALL","<b>" + callTime +" UTC-6</b>");
                    //if(template.includes("PH")) template = template = template.replace("PH","<b>" + cPhone +"</b>");
                    if(template.includes("TICKET")) template = template.replace("TICKET","<b>" + cNumber + "</b>");
                    //if(template.includes("DESC")) template = template.replace("DESC", "<b>" + cDes + "</b>");

                    document.getElementById("followUp").innerHTML = template;//.replace(/\n/g,"<br>");
                }

                async function GenerateFollowUp(stab=false){
                    const cName = document.getElementById("clientNameF").value;
                    const cNumber = document.getElementById("caseNumF").value;
                    const cPhone = document.getElementById("clientPhoneF").value;
                    const cDes = document.getElementById("followDes").value;
                    const timeReference = document.getElementById("callTime").value;
                    const slaLanguage = document.querySelector('input[name="language"]:checked').value;
                    const strikeNum = document.querySelector('input[name="sNum"]:checked').value;

                    //#region 12 Hours format conversor
                    let [h, min] = timeReference.split(':');
                    let hours = parseInt(h,10);
                    let dayzone = '';
                    if(hours === 0){
                        hours = 12;
                        dayzone = 'am';
                    }
                    else if(hours === 12){
                        dayzone = 'pm';
                    }
                    else if(hours > 12){
                        hours -= 12;
                        dayzone = 'pm';
                    }
                    else{
                        dayzone = 'am';
                    }
                    let callTime = hours+":"+min+dayzone;
                    //#endregion


                    if(slaLanguage === "eng"){
                        if(stab){
                            template = await GetTemplates("FU_STL_ENG");
                        }
                        else{
                            if(strikeNum === "1") template = await GetTemplates("FU_S1_ENG");
                            else if(strikeNum === "2") template = await GetTemplates("FU_S2_ENG");
                            else if(strikeNum === "3") template = await GetTemplates("FU_S3_ENG");
                        }
                    }
                    else if(slaLanguage === "esp"){
                        if(stab){
                            template = await GetTemplates("FU_STL_ESP");
                        }
                        else{
                            if(strikeNum === "1") template = await GetTemplates("FU_S1_ESP");
                            else if(strikeNum === "2") template = await GetTemplates("FU_S2_ESP");
                            else if(strikeNum === "3") template = await GetTemplates("FU_S3_ESP");
                        }
                    }

                    if(template.includes("NAME")) template = template.replace("NAME",cName);
                    if(template.includes("TIME")) template = template.replace("TIME","<b>" + callTime +" UTC-6</b>");
                    if(template.includes("PH")) template = template = template.replace("PH","<b>" + cPhone +"</b>");
                    if(template.includes("TICKET")) template = template.replace("TICKET","<b>" + cNumber + "</b>");
                    if(template.includes("DESC")) template = template.replace("DESC", "<b>" + cDes + "</b>");
                    
                    document.getElementById("followUp").innerHTML = template;//.replace(/\n/g,"<br>");
                }

            </script>
        </div>

        <!--Closure-->
        <div id="tab5" class="tab-content">
            <div>
                <legend style="display: block">Feedback URL</legend>
                <input type="text" id="Feedback" placeholder="Here goes the survey url" style="display: block">
                <legend>Symtom:</legend>
                <textarea id="cSymptom" style="width: 750px; height: 100px;"></textarea><div></div>
                <legend>Cause:</legend>
                <textarea id="cCause" style="width: 750px; height: 100px;"></textarea><div></div>
                <legend>Solution:</legend>
                <textarea id="cSolution" style="width: 750px; height: 100px;"></textarea><div></div>
            </div>

            <div>
                <button id="closure" onclick="GenerateClosure()">Generate Closure</button>
                <script>

                    function selectText() {
                        const element = document.getElementById("closureResult");

                        if (document.body.createTextRange) { // Para IE
                            var range = document.body.createTextRange();
                            range.moveToElementText(element);
                            range.select();
                        } else if (window.getSelection) { // Para navegadores modernos
                            var range = document.createRange();
                            range.selectNodeContents(element);
                            var selection = window.getSelection();
                            selection.removeAllRanges();
                            selection.addRange(range);
                        }
                    }

                    async function GenerateClosure(){
                        const cName = document.getElementById("clientNameF").value;
                        const cNumber = document.getElementById("caseNumF").value;
                        const Survey = document.getElementById("Feedback").value;
                        const cSurvey = `<a href="${Survey}" target="_blank">${Survey}</a>`;
                        const cSympt = document.getElementById("cSymptom").value;
                        const cCause = document.getElementById("cCause").value;
                        const cSolution = document.getElementById("cSolution").value;
                        const cLanguage = document.querySelector('input[name="language"]:checked').value;

                        if(cLanguage === "eng"){
                            if(Survey !== "") template = await GetTemplates("CL_LN_ENG");
                            else template = await GetTemplates("CL_ENG")
                        }
                        else if (cLanguage === "esp")
                        {
                            if(Survey !== "") template = await GetTemplates("CL_LN_ESP");
                            else template = await GetTemplates("CL_ESP")
                        }

                        if(template.includes("NAME")) template = template.replace("NAME",cName);
                        if(template.includes("TICKET")) template = template.replace("TICKET","<b>" + cNumber + "</b>");
                        if(template.includes("LINK")) template = template.replace("LINK",cSurvey);
                        if(template.includes("SYNT")) template = template.replace("SYNT",cSympt);
                        if(template.includes("CAUSE")) template = template.replace("CAUSE",cCause);
                        if(template.includes("SOL")) template = template.replace("SOL",cSolution);

                        document.getElementById("closureResult").innerHTML = template.replace(/\n/g,"<br>")
                    }
                </script>
            </div>
            <fieldset>
                <legend>Generated Template</legend>
                <div class="generated-text" id="closureResult" onkeydown="" onpaste="return false" contenteditable="true"></div>
                <script>
                    let CSctrl = false;
                    let CSe = false;

                    document.getElementById("closureResult").addEventListener("keyup",function(event){
                        if(event.ctrlKey){
                            ctrl=true;
                        }
                        if(event.key === 'c' || event.key === 'C'){
                            event.preventDefault();
                            e = true;
                        }
                    });

                    document.getElementById("closureResult").addEventListener("keydown", function(event) {
                        //const selectedText = window.getSelection().toString();
                        if(event.ctrlKey){
                            ctrl=true;
                        }
                        else{
                            event.preventDefault();
                        }
                        if(event.key === 'c' || event.key === 'C'){                            
                            e = true;
                        }
                        else{
                            event.preventDefault()
                        }

                        if(ctrl && e){

                        }
                    });
                </script>
            </fieldset>
        </div>

        <script>

            const subButtons = document.querySelectorAll('.subtab-btn');
            const subContents = document.querySelectorAll('.subtab-content');
            const buttons = document.querySelectorAll('.tab-btn');
            const contents = document.querySelectorAll('.tab-content');

            subButtons.forEach(button => {
                button.addEventListener('click', () => {
                    // Quitar clase activa de todos
                    subButtons.forEach(btn => btn.classList.remove('active'));
                    subContents.forEach(content => content.classList.remove('active'));

                    // Activar el seleccionado
                    button.classList.add('active');
                    document.getElementById(button.dataset.tab).classList.add('active');
                });
            });

            buttons.forEach(button => {
                button.addEventListener('click', () => {
                    // Quitar clase activa de todos
                    buttons.forEach(btn => btn.classList.remove('active'));
                    contents.forEach(content => content.classList.remove('active'));

                    // Activar el seleccionado
                    button.classList.add('active');
                    document.getElementById(button.dataset.tab).classList.add('active');
                });
            });
        </script>
    </body>
</html>