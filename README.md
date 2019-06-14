BSD 3-Clause License

Copyright (c) 2019, Quality Technology Services, LLC
All rights reserved.

# QTS API

# Overview 
QTS, a leading provider of software-defined and mega scale data center solutions, has published its RESTful application programming interface (API) definitions as Open Source on Github under the BSD license to provide maximum transparency and offer as a basis of an open standard for the industry. 

QTS is committed to the digitization of its end-to-end systems and processes to enable new ways for its customers and employees to interact with QTS’ platform and accelerate the deployment of new services and solutions. 

The JSON for RESTful APIs can be accessed are contained in the Postman Collection file:
https://github.com/qtsdatacenters/qts_api/tree/master/Postman%20Collections

These APIs support real-time access to all aspects of data center operations including security, power, cooling, sensors, provisioning and many other key metrics. This allows customers to embed the API into their own systems allowing increased control and visibility of their data center footprint.

If you have questions or need support, please contact apisupport@qtsdatacenters.com

API Requests and Version Control
QTS API URLs use the following naming convention which includes the version: https://api.qtsdatacenters.com/sdp/api/{modulename}/v1/{resourcePath}

The version of the RESTful API is noted in the URL.

QTS will up-version API based on major changes including:

* a change in the format of the response
* a change in the response/data type
* removing any part of the API

#Common API Responses
APIs use standard HTTP status codes to indicate the general result of a request. Every response from the API will have one of the following status codes in the header:

HTTP Code	Reason	Description
200 OK		Success. Indicates that the request was valid and the transaction executed normally.
201 Created		Indicates that a new resource was created. 201 Created is not used when a resource is updated.
400 Bad Request	INVALID_INPUT_DATA	Indicates that the JSON posted with the request contained bad syntax or was missing required fields.
401 Unauthorized	AUTHORIZATION_FAILURE	Indicates invalid credentials were provided for authentication.
403 Forbidden	FORBIDDEN	Indicates that the credentials provided for authentication were valid but the user is not permitted to access the resource.
404 Not Found	RESOURCE_NOT_FOUND	Indicates that there is no resource at the URI specified in the request.
405 Method Not Allowed		Indicates the URL is valid but operation is not supported or not allowed for the resource.
408 Request timeout		Indicates the request response was not received during expected amount of time.
500 Internal Server Error	UNEXPECTED_ERROR	Indicates that a general error has occurred with the request that is not described by another status code.
503 Service Unavailable		System is down for scheduled maintenance.This response remains in place for the duration of the scheduled maintenance window.
400 Bad Request	INVALID_INPUT_DATA	Indicates input data does not correspond to validation rules or mandatory fields were missed.
Paging, Filtering and Sorting
Paging is the act of splitting a large list of results into consumable chunks as a means of improving performance and general manageability.

Paging operators are separated with & symbol
Paging operators are case sensitive
The following operators are supported:

pageNumber - specify number of pages
pageSize - number of records per page
Example: /sdp/ticketing/v1/api/temporary_visitor_reservations?pageNumber=1&pageSize=20

Optional Paging Parameters
Parameter	Usage	Example
pageSize	Optional. May only be specified once. Must be a positive integer between 1 and a maximum value documented for each API function.	pageSize=50 Return 50 records per page
pageNumber	Optional. May only be specified once. Must be a positive integer. If the value supplied exceeds the number of pages for the request, the last page will be returned.	pageNumber=3 Returns the 3rd page. If pageSize is 50, then return records 101-through-150.
JSON response elements when using paging parameters:

Attribute Name	Attribute Details	Description
pageCount	Non-negative Integer	The number of result records in this page.
pageNumber	Non-negative Integer	The page number requested by the user or the default page (1) if none supplied.
pageSize	Non-negative Integer	The page size used; either specified in the request or the default for the API function being requested, max value 10000.
totalCount	Non-negative Integer	The total number of result records matching request criteria
Example result:
{
"pageInfo": {
"pageSize": 20,
"totalCount": 115,
"totalPages": 6,
"pageNumber": 1
},

Filtering and Sorting
Use sorting to present results in a specified order.

Sort keyword specifies the parameter results should be sorted.
Name of request parameter must be specified to identify how results should be sorted
Sorting operators:
ASC
DESC
Sorting operator and keywords are case sensitive
Example: /sdp/ticketing/v1/api/temporary_visitor_reservations?sort=location.ASC

Use filtering to reduce size of list by applying request criteria to narrow the matched set of results.

The following general rules apply across all APIs developed with the paging and filtering approach:

Every GET method has its own set of filter parameters documented
Parameter names and some parameter values are based on keywords and field names, which will be published in the API documentation
Field names are case sensitive
The set of field names and filters for a given API function can be enhanced over time with the publication of new values
Multiple filtering operators are separated with & symbol
Dates are defined in XSD format but can be passed in full (including time) or short (date only) format. For example; "20180901T00:00:00Z" and "2018-09-01" equate to the same value and time zone.
Both EQUALS (single value) and IN (multiple value) queries are supported
The filter component has the following format: ?_s=field==value where the filter criteria are specified as field=value pairs.
Logical Operators	Syntax	Description
AND	;(Semicolon)	All conditions in the filter parameter must be true. Example:?_s=firstName==john;lastName==lee Returns Roster Users with first name as "john" AND last name as "lee"
OR	,(Comma)	Any of the conditions specified in the filter parameter must be true. Example: ?_s=firstName==john,lastName==lee Returns Roster Users with first name as ‘john’ OR last name as ‘lee’
Comparison Operators	Syntax	Description
EQUAL	==	The result must be equal to the value specified in the field=value pair. Example: ?_s=username==john.test Returns only Roster Users that have ‘john.test’ as username
NOT EQUAL	!=	The result must not be equal to the value specified in the field=value pair. Example: ?_s=username!=john.test Returns only Roster Users that do not have ‘john.test’ as username
GREATER THAN	gt	The result must be greater than the value specified in the field=value pair.?_s=dateCreation=gt=2018-09-01 Returns only records created after 1st of September 1, 2018
LESS THAN	lt	The result must be less than the value specified in the field=value pair.?_s=dateCreation=lt=2018-09-01 Returns only records created before 1st of September 1, 2018
Additional Operators	Syntax	Description
Wildcard	*	The “*” wildcard character (asterisks) can be used to match zero or more characters. Each API supporting wildcard states that it supports wildcard within its own definition.
STARTS WITH	==	Example: ?_s=username==john Returns only Roster Users that has username starts with "john"
ENDS WITH	==*	Example: ?_s=username==*john Returns only Roster Users where username ends with "john"
CONTAINS	==*value*	Example: ?_s=username==*john* Returns only Roster Users that has "john" in username
ORDER BY	orderBy=value	?orderBy=username Results would be sorted by username, ascending order by default
PAGING PARAMETERS	pageNumber=value;pageSize=value	?pageNumber=2;pageSize=10 Returns only second page, 10 results per page
Sample Formatting Requests
Request	Response
/sdp/api/roster/v1/users	Lists 100 Users that are available for the current requester, sorted by username
/sdp/api/power/v1/raw_data?site=DFW1&format=json& _s=period==monthly;date==10-01-2018	Generates JSON raw power data report for monthly period October 2018. Report contains all circuits and poles of DFW1 site including kW readings for poles of specified location circuits
/sdp/api/power/v1/power_demand?site=DFW1&_s=date==12-01-2018;period==weekly;site=DFW1	Generates .xlsx report for week starting December 1, 2018 for DFW1 site
/sdp/api/roster/v1/users?pageNumber=2;pageSize=100;orderBy=date	Indicates a page size of 100 and requests the second page of results being page 101 to 200 inclusive, ordered by date of deployment ascending (default)
Paging and Filtering Common API Responses
HTTP Code	Reason	Description
400 (Error)	INCORRECT_PAGING_PARAMETER	A supplied value for any paging parameter is outside of accepted value set. A negative or non-numeric pageSize or pageNumber, pageSize too large, an invalid orderBy value or more than one value was supplied for a given paging parameter.
400 (Error)	INVALID_FILTER_VALUE	A filter was specified with an invalid value. Returned if an invalid comparative operator is used with a filter field. For example, MIN and MAX will typically not be allowed with String fields.
400 (Error)	UNSUPPORTED_PARAMETER	Invalid comparative operator
400 (Error)	INVALID_INPUT_DATA	Not a comparison expression. Returned in case any filtering or sorting parameter was missed or extra symbols added to the filter line.
400 (Error)	TOO_MANY_FILTER_PARAMETERS	Exceeded maximum supported number of filter operators. Maximum number of filter operators is 10. Returned if more than 10 values for a filter line were supplied.
Language
