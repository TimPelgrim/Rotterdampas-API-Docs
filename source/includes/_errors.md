# Errors and status codes
The API should respond using one of the following status codes to indicate whether the call was successful or whether something went wrong.


Error Code | Meaning
---------- | -------
200 | Everything is a-ok.
201 | Created.
204 | No Content -- Used as repsonse to a successful delete.
400 | Bad Request -- You are missing parameters.
401 | Unauthorized -- You are not allowed to see this because your API Key is incorrect.
403 | Forbidden -- You are not allowed to see this because the data is not for you.
404 | Not Found -- One of the specified IDs does not exist.
500 | Internal Server Error -- Server troubles.

<aside class="success">
Remember -- 304 etc is used for Caching!
</aside>
