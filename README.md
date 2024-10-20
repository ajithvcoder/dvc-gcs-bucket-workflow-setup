### Using Service Account method with DVC in GIthub CI/CD pipeline

**Content**

1. Use gdrive storage

2. Use GCS bucket storage


References:

**Use GC bucket storage**

In this method we can store data in google cloud storage bucket and fetch using serice account authentication

To create a service account, navigate to IAM & Admin in the left sidebar, and select Service Accounts. 

![service_account_icon](./assets/snap_tutorial_1.png)


Click + CREATE SERVICE ACCOUNT, enter a Service account name e.g. "My DVC project"
if you are new and dont know what permission to chose better give owner permission

![owner-permission](./assets/snap_tutorial_2.png)

Give all user account for which u need to grant access

![email-access](./assets/snap_tutorial_3.png)

 Then click CREATE AND CONTINUE. 
 Click DONE and you will be returned to the overview page. 
 
Now you can see your service account and click it. Go to the Keys tab. 

![serivce-mail-id](./assets/snap_tutorial_4.png)
 
Under Add key select Create new key, choose JSON, and click CREATE. 

![key-creation](./assets/snap_tutorial_5.png)
 
Download the generated projectname-xxxxxx.json key file to a safe location. 

#TODO: change to numbers



- Store the api key in local folder as credentials.json but dont commit it to github. if u do so github will raise a warning but inturn google gets notified and revokes the credentials.

- In google console search bar type "Google cloud storage" and go there

- Click "Create" button -> Give a name to bucket -> In "Choose where to store data" select "Region" -> "asia-south1" (it can be anything just for example i told) -> Click what ever option that is default after this. -> Click "Create" at last


![](./assets/snap_create_button.png)

![](./assets/snap_create_2.png)


- Click "Create a folder" and then give a name "storage" (you can give any name) 

![](./assets/snap_create_folder.png)

Now you would have got a folder similar to screenshot below

![](./assets/snap_create_folder_2.png)

So the url or the location of this is gs://<bucket_name>/<folder_name> so for above folder it is "gs://dvctestbucket/storage"  where dvctestbucket is bucket name and storage is folder name.


**Here after in you local we need to handle and all you need is json file (dvcmanager-38xxxxxxx.json/any json with projectname-xxx.json which you downoaded as key) and google storage url (gs://dvctestbucket/storage)**

- Create a folder "data" in local

- Copy the contents from this https://www.kaggle.com/datasets/khushikhushikhushi/dog-breed-image-dataset to data folder and unzip it. Remove all files that are not needed eg: archive.zip is not needed after unzipping.

|- data
|----dataset
|--------Beagle
|--------Boxer
|--------.
|--------.

- Install dvc and dvc-gs

```pip install dvc dvc-gs```

- Run git init (if you are not in a git folder already)

- Run dvc init

- Now run dvc remote add -d myremote gs://<mybucket>/<path> command. Reference https://dvc.org/doc/user-guide/data-management/remote-storage/google-cloud-storage 

eg:  ```dvc remote add -d myremote gs://dvctestbucket/storage````

You can see that in .dvc file "myremote" would have got added

![dvc config](./assets/snap_myremote.png)

- Run ```dvc remote modify --local myremote credentialpath devcmanager-385390fe7f4f.json``` i.e ```dvc remote modify --local myremote credentialpath 'path/to/project-XXX.json'```

- Run ```dvc add data```  i.e `dvc add <data_folder_name>

- Run ```dvc config core.autostage true``` (optional)

- Run ```dvc push -r myremote -v```

- Wait for 10 minutes if its like 800 MB data for pushing and if its in github actions wait for 15 minutes.

Now you can check your google cloud bucket you would have a folder "files" like this

![](./assets/snap_files_folder.png)

**Github Actions**

- Now push this to github repo

Note in .dvc folder by default u can push only "config" and ".gitingore" file. Dont change it let it be like that.

- Kindly note it dont ever push "project-xxx.json" file. if you push google will identify it and revoke the token you need to set the key again.

- Add `.dvc/config`, `.gitignore`, `data.dvc` files alone.



 
**Reference**

Refered 1st point alone in"Using service account" in https://dvc.org/doc/user-guide/data-management/remote-storage/google-drive#using-service-accounts