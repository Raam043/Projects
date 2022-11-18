# Project :- CDN ( Content Delivery Network) using Cloudfront



## `Option 1` - for Public Bucket and Objects 


### 1.  Release `S3 bucket` to store content


![Screenshot 2022-11-13 at 1 39 27 PM](https://user-images.githubusercontent.com/111989928/201512338-fd3da6c9-bb8c-460e-bc87-6fd285d3f15f.png)


![Screenshot 2022-11-13 at 1 42 48 PM](https://user-images.githubusercontent.com/111989928/201512365-494c8d09-af45-403a-881c-ae9b929ef023.png)


Turn on Publicj view

![Screenshot 2022-11-13 at 1 43 14 PM](https://user-images.githubusercontent.com/111989928/201512377-c52e20f9-36cd-41d8-8de5-f5461f6d4628.png)


Open the Bucket and add content
![Screenshot 2022-11-13 at 1 43 54 PM](https://user-images.githubusercontent.com/111989928/201512399-05b835ba-5262-4b12-9ddd-07c71c37b391.png)


![Screenshot 2022-11-13 at 1 48 08 PM](https://user-images.githubusercontent.com/111989928/201512529-f2a8520c-2508-4f4c-ad97-3f44124daf74.png)

Grant Public Access

![Screenshot 2022-11-13 at 1 48 44 PM](https://user-images.githubusercontent.com/111989928/201512559-955f2004-e7c3-4c05-8b5b-ed5ab2828ac7.png)





https://s3.us-east-2.amazonaws.com/ramesh.in/index.html

![Screenshot 2022-11-13 at 1 59 45 PM](https://user-images.githubusercontent.com/111989928/201512904-cad267de-e5b3-407d-9449-7871ee613bb0.png)



### 2. Create CloudFront Distribution

Browse the S3 bucket which is created above

![Screenshot 2022-11-13 at 2 01 13 PM](https://user-images.githubusercontent.com/111989928/201512955-ee47f17e-ca60-4c33-8057-8fd593885b1d.png)

`Select the Root file`

![Screenshot 2022-11-13 at 5 00 35 PM](https://user-images.githubusercontent.com/111989928/201519433-a39c1da2-0cf5-4dfa-a554-2e9c3e59f0e8.png)


After Creation it will generate the defualt Domain for content

![Screenshot 2022-11-13 at 2 08 29 PM](https://user-images.githubusercontent.com/111989928/201513236-a8efe225-36f4-462b-9ae0-2cc8bdef8ca1.png)



Copy the Domain and open it on new tab to veryfication

![Screenshot 2022-11-18 at 11 00 20 PM](https://user-images.githubusercontent.com/111989928/202766648-3c287850-db28-4e70-82a4-e087def722f1.png)



###  3. Create `Error Page` if content missed or under deployment progress.

![Screenshot 2022-11-13 at 2 11 23 PM](https://user-images.githubusercontent.com/111989928/201513354-7e165941-8521-4924-bca6-4ea8f2053d07.png)



## `Option 2` - for Private Bucket and Objects can be accessed by only `CloudFornt`


### 1 - Create Private S3 Bucket with files ( Disable the public accees)

