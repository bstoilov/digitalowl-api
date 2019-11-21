**This is deprecated, out of date and will soon be deleted, use the python client instead https://github.com/bstoilov/digitalowl-pysemantics**



**<a href='https://digitalowl.org/'> Digital Owl</a> REST API documentation**
----
<br/><br/><br/>

Go to this <a href='https://digitalowl.restlet.io/'> link</a> to view a better and up to date documentation. 


**Text Classification Api**
----
  Returns tags(labels) for text, or url, based on its content meaning.
  Same input field is used both for plain text requests and for url, the server will recognize urls, download them, extract the meaningful text from them (it will exclude thing like navigation, menus, cookie consents , etc) and run same classification algortihm on the text.

* **URL**

  https://digitalowl.org/api/classify/topic

* **Method:**

  `POST`
 
* **Headers:**

  `Content-type: application/json` <br />
  `api_key: <api key generated upon registration>` <br />
  The api key is optional, but without it you might hit some request limit.<br />

* **Url body example**
 ```javascript
  {
     "input":"http://iamafoodblog.com/pho-strami-a-pho-forward-take-on-pastrami/"
  }
  ```
 * **Text body example**
 ```javascript
  {
     "input":"<some big text>"
  }
  ```

* **Example success response:**

  * **Code:** 200 <br />
    **Content:**
    ```javascript
     {
       "tags":["food","cooking"],
       "originalTags":["grilled cheese sandwiches","recipes","cooking","food"],"message":"OK"
     }
    ``` 
    `tags` - These are generalized labels, they are generalized by hand and should be used as final result.<br />
    `originalTags` - these are the actual labels returned by the algorithm, in most cases they are too specific and should not be trusted blindly, you can even chose to ignore them. <b> However they belong to the same general category as the provided text.</b> <br />
    
    Original tags are returned so we can easily track wrong classifications. The idea is that you text in this case a cooking recipe, is about some chinese dish, but the too specific tag states that it is `grilled cheese sandwiches`, it also had guessed correctly `recipes`. Based on these tags our generalization logic correctly generalizes the text as food and cooking. You can view the original tags as some kind of meta on how the tags are chosen, but not as classification labels for the inpit.
    
 
* **Error Response:**

  * **Code:** 400 Bad Request <br />
    **Content:** `{ error : "Failed to download url" }`<br />
    If the provided url can not be dowloaded by the server


  * **Code:** 403 Forbidden <br />
    **Content:** `{ error : "You have reached the maximum allowed requests for this IP." }`
    There is a maximum nuber of daily requests you can peform with ip. <b> This won't affect you if you provide your api key header in the requests</b>
    
   * **Code:** 500 Internal error <br />
    **Content:** `{ error : "<one of various reasons>" }`<br />
    This is most likely a problem on our side and you can report it as a bug.
    
* **Notes:**
  * Keep in mind that due to the nature of machine learning algorithms, small text inputs could lead to incorect classifications. This also included sites with little to no contents. This doean't mean that it will always be incorect just that the chance of correct classification is smaller
  * If you see incorect classification, you can report it directly in the UI, or you can email us, doing this will make our service better.


**Sentiment analysis Api**
----
  Returns overeal attitude of some text, can be negative, positive or neutral.  Works best for smaller texts.

* **URL**

  https://digitalowl.org/api/classify/sentiment

* **Method:**

  `POST`
 
* **Headers:**

  `Content-type: application/json` <br />
  `api_key: <api key generated upon registration>` <br />
  The api key is optional, but without it you might hit some request limit.<br />

* **Body example**
 ```javascript
   {
      "input":"God I love these shoes!! They are perfect fit and also arrived on time."
   }
 ```

* **Example success response:**

  * **Code:** 200 <br />
    **Content:** <br />
     ```javascript
    {
       "sentiment":"negative",
       "similarity":0.780967116355896
    }
    ```
    `sentiment` - The overal mood of the text.<br />
    `similarity` - How certain we are in the response <br />
        
 
* **Error Response:**

  * **Code:** 403 Forbidden <br />
    **Content:** `{ error : "You have reached the maximum allowed requests for this IP." }`
    There is a maximum nuber of daily requests you can peform with ip. <b> This won't affect you if you provide your api key header in the requests</b>
    
   * **Code:** 500 Internal error <br />
    **Content:** `{ error : "<one of various reasons>" }`<br />
    This is most likely a problem on our side and you can report it as a bug.
 
 
 **Text similarity Api**
----
  Evaluates how similar two texts are, <b>based on their meaning not syntatics</b>. Two texts can be writen in very different style and completely different words can be used, but they can for example both be about, politics in EU making them very similar by topic, our service will rank these as very similar. 

* **URL**

  https://digitalowl.org/api/classify/similarity

* **Method:**

  `POST`
 
* **Headers:**

  `Content-type: application/json` <br />
  `api_key: <api key generated upon registration>` <br />
  The api key is optional, but without it you might hit some request limit.<br />

* **Body example**
 ```javascript
    {
       "input":"Eating the apple raw is good for your health.",
       "secondInput":"My medical condition is good because i consume pear every day."
    }
 ```

* **Example success response:**

  * **Code:** 200 <br />
    **Content:** <br />
     ```javascript
    {
       "similarity":0.7730205953121185
    }
    ```

    `similarity` - Everything above `0.5` can be considered as similar<br />
        
 
* **Error Response:**

  * **Code:** 403 Forbidden <br />
    **Content:** `{ error : "You have reached the maximum allowed requests for this IP." }`
    There is a maximum nuber of daily requests you can peform with ip. <b> This won't affect you if you provide your api key header in the requests</b>
    
   * **Code:** 500 Internal error <br />
    **Content:** `{ error : "<one of various reasons>" }`<br />
    This is most likely a problem on our side and you can report it as a bug.


**Word analysis Api**
----
This api can find contextually similar words. Or words that often appear in the same context.

* **URL**

  https://digitalowl.org/api/wv/eval

* **Method:**

  `POST`
 
* **Headers:**

  `Content-type: application/json` <br />
  The api key not needed here, we are no planing to limit the usage of this in any way.<br />

* **Body example**
 ```javascript
    {
        "positive":["king","woman"],
        "negative":["man"]}
    }
 ```
You can view this as equation of words. We are asing the api `What is a king, it is a woman but it is not a man`, the most accurate answer to the above is "queen"

* **Example success response:**

  * **Code:** 200 <br />
    **Content:** <br />
     ```javascript
    {
        "words": {
               "queen":0.7698540687561035,
               "throne":0.6755737066268921,
               "monarch":0.6843380928039551
              }
    }
    ```

    `words` - Are the contextual synonyms of the equation build from negative and postive words <br />
        
 
* **Error Response:**

   * **Code:** 500 Internal error <br />
    **Content:** `{ error : "<one of various reasons>" }`<br />
    This is most likely a problem on our side and you can report it as a bug.



If you have furhter questions or something in the API documentation is not clear, do hasitate to contact us directly on github or on our <a href="https://digitalowl.org/#contact">home page contact section</a>.


    



