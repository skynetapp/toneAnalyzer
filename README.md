# toneAnalyzer
Tone Analyzer Work Flow:<br>

The Folder structure is as follows:<br>
index.php<br>
Global -> DBmongo(Mongo Connection ),DBmysql(MySQL Connection ), Tone Analyzer API Connection. <br>
Lib -> Smarty, common functions.<br>
Modules -> BluemixToneAnalyzer -> Controller, Action, DB MySql, View, DB Mongo.<br>
Views -> BluemixToneAnalyzer -> Header.tpl,Footer.tpl, master.tpl,detailList.tpl.<br>


In order to insert or get data from DB flow is index.php -> Controller -> Action -> MySql.<br>
In order to view the output flow is index.php -> Controller -> View.<br>
index.php -> Created to interact with Tone Analyzer API and to perform Lytepole Analytics operations.<br>
Controller -> Controlles all the operations of Tone Annalyzer module.<br>
Action -> Created to perform various operations specified in controller.<br>
MySql -> Created to connect to MySql DB and perform various Sql operations.<br>
view -> Created to pass the data to tpl files.<br>
Depending on the module and action will perform various cases.<br>

Steps:<br>
1. Connecting to Bluemix with admin credentials.<br>
2. Getting multiple reocrds data from Request table DB and calling CURL of Tone Analyzer API.<br>
3. On receiving JSON response from API, inserting JSON response in Mongodb.<br>
4. Store the response data in Master and child tables accordingly.<br>
