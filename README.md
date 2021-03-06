#### Date: 21-10-2016
#### Description: This document aims to define the Bluemix Tone Analyzer API coding 


#### The Folder Structure is as follows:
   
   
   Root Directory | Sub Directory | Sub Directory 
------------ | ------------- | -------------
index.php | | |
Global | DBmongo(Mongo Connection),DBmysql(MySQL Connection), BlueMixToneAnalyzerAPI(Tone Analyzer Connection)  | 
Lib | Smarty,Common functions | |
Modules | BluemixToneAnalyzer | Tone Analyzer Controller, Tone Analyzer Action, Tone Analyzer MySql, Tone Analyzer View, Tone Analyzer API Call, Tone Analyzer DB Mongo|
Views | BluemixToneAnalyzer | header.tpl, footer.tpl(Common files), masterList.tpl,detailList.tpl|

#### Architecture

1. Read data from MySQL Table where ToneAnalyzerStatus='' and ToneAnalyzer=0 (Table name - BlueMixAlmEntityExtractReq)
2. Invoke Watson by calling Tone Analyzer API which calls the curl and gets response from API for multiple records.
3. The response is processed and updated in Parent Table (master_tone_analyzer_request) and the corresponding children are stored in child table name (children_tone_analyzer_request). 
4. Updating the processed records status after getting the response. 
5. The raw response from Watson is also stored in MongoDB (lytepole) as raw JSON file.


#### Code Flows as follows:
   * To insert or get data from DB code flows.. index.php -> Controller -> Action -> MySql.
   * To view the data code flows.. index.php -> Controller -> View.
   
 
#### Step 1:
  Add the created Url, Username and Password in the config.php under bluemix2.0 folder.we can get the Tone Analyzer API Username and password by logging into IBM Bluemix. 
	
**_Code:_**
	
```
	
	$GLOBALS['bluemix_toneanalyzer_username']='xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx';
	$GLOBALS['bluemix_toneanalyzer_password']='xxxxxxxxxxxx';
	$GLOBALS['bluemix_toneanalyzer_url']='https://gateway.watsonplatform.net/tone-analyzer/api/v3/tone?version=2016-05-19';
	
```
	
  
#### Step 2:
  Create a module name as 'toneAnalyzer' in index.php and respective actions will be performed accordingly.
  

#### Step 3:
   In the server normally we will be able to see existing Master list data. When we click on the update button 'InsertList' action will be performed from index page and **insertBlueMixToneAnalyzerResponseIntoDB()** will be called.
   
#### Step 4:
   From index.php, **BluemixToneAnalyzerController** class will be called which controlles all the operations of Tone Analyzer module. Here function **insertBlueMixToneAnalyzerResponseIntoDB()** will be executed.
   
#### Step 5:
   This **getTextData()** function gets the multiple records data from Request table and sends request to Tone Analyzer Curl response function **getBlueMixCURLResponse($text)** one by one using for loop.
   
#### Step 6:
   On receiving response from API, the JSON response will be stored in Mongo DB by calling function  **insertBlueMixJSONResponseIntoMongo($data)**.
   
#### Step 7:
   The JSON response result will be converted into Array by calling function **transformJSONToArray($data)**.
   
**_Code:_**

```   
   public function transformJSONToArray($sampletext){
    	$response_array = json_decode($sampletext,TRUE);
    	return $response_array;
	}
  
```  

#### Step 8:
   The response array will be inserted into Master data by function **insertMasterDataIntoMySQL($bluemix_response_array)**.
   

#### Step 9:
   Here based on the master request id, the response will be stored in Child table by function **getParsedDataFromJSONResponse($masterReqId)**.



#### Step 10:
   On inserting the JSON response into master and child tables, the status and Request date will be updated for that record in the Request table by function **updateToneAnalyzerTextData($id)** in controller.


#### Step 11:
   To get the Master list function **getALLMasterDataFromMySQL** will be called from controller.
   
#### Step 12:
   To view the Master list function **showallMasterListView()** will be called from controller to View.
   
**_Code:_**

```
   function showallMasterListView($data_arr){
        $smarty = new Smarty();
        $smarty->assign('base_path',$GLOBALS['base_path']);
		$smarty->assign('cursor',$data_arr);
	    $smarty->display(''.$GLOBALS['root_path'].'/Views/BluemixToneAnalyzer/allmasterList.tpl');
    }
    
``` 

#### Step 13:
   To view the Child data based on the master id, function **getChildDataFromMySQL($post_data)** will be called from controller.
   Function **getAllChildDataFromMySQL($post_data)** will get the reocrds based on the master id using MySql query. Function **showDetailListView($bluemix_list_vo)** will be called in view. 
   
 
#### Assumptions

- DBMongo - Inserts the Alchemy JSON response into mongo. It is included in Global -> DBMongo.


#### Errors

If any erros found, the following may be the reasons.

- Url might not be correct. It should be as below.

**_Url:_**

```
http://159.203.239.91/bluemix2.0/index.php?module=toneAnalyzer&action=GetList

```
- The root path, base path and the database name should be correct in config.php.
- The tone analyzer url, username and password should be a valid from IBM Bluemix. 
- If the day limit is exceeded for the API key, then also the child results will not be inserted. 

   
#### MySQL Database Details

  
 Database Name: bluemix
 
 Tables | Description | Fields 
------------ | ------------- | ------------
BlueMixAlmEntityExtractReq | Request table to get records where ToneAnalyzerStatus='' and ToneAnalyzer=0 | |
master_tone_analyzer_request | Stores the extracted data based on the request id | alm_id, alm_request_date, alm_external_id, alm_emotion_response_text |
children_tone_analyzer_request | Stores the child records based on master id | children_id, master_request_id, tones_id, tones_tone_name, category_id, children_date_created |
 
 
#### Mongo Database details
 
- Database Name: lytepole
- Table or collection name: BlueMixPersonalityExtract
- Description: Mongo stores the JSON response given by the Alchemy API for all the records.

- To start the mongoDB, open putty.
- To start the MongoDB service type command - **mongo**.
- To display the database, type command **db**. It will return **test** as default database. To use our database type command **use dbname**.
- To stop the MongoDB service type command - **exit**.
