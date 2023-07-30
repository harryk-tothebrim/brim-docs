Based on Brim's Confluence
#### Module knowledge
This project is Nx based monorepo project contains apps and libs.

Nx follow a 80/20 approach, place 80% of your logic into the libs/ folder

and 20% into apps/.  
<br>  

 
### Apps
#### app
App is the start point of flutter application, it includes mobile application as well as web application. App is currently working on android as well as ios platforms. Web part of app is admin portal 

To run the web try:  
`flutter run -d chrome`
 <br>

#### graph
Graph is the start point to graphql endpoint of backend service. It contains all code related to logic, calculations and mongodb. We are currently using typegoose for mongodb operations. 

To run the graph try:  
`nx run graph:serve`  

we are using typegoose to create models, and graphql. To generate types in graphQL you can run:  
`nx run grapher:generate-graphql-types`  
Although by doing graph:serve it will create types as well as start the server.
<br>  
 

#### ocpp
OCPP is the central system for chargers. It is also a typescript based app which contains connection with chargers. Charger events will be responded to by ocpp server.  
<br>  
 

#### notifications
Notifications is express app which use REST protocol. It is being used to send notifications in telegram. The notifications are system alerts which gets pushed into pubsub and this pulls the pubsub message and acknowledge message. Notifications endpoint is configured in cloud scheduler.  
<br>  
 

#### sites-operations
Sites operations is another app in Nx which is responsible for cron jobs. These jobs include system health check, send alerts and load balance chargers. This is also configured in cloud scheduler. 

To run the sites-operations try:  
`nx run sites-operations:serve`    
<br>  


#### charger-01-z
Charger-01-z is app responsible for testing the behaviour of the charger in real time. The script have some predefined use case of chargers and can add as we get to know ultimate behaviour of chargers.

To run charger-01-z try:  
`nx run charger-01-z:serve`  
<br>   


 
#### Rest
Rest is a app in Nx workspace which is responsible for manually sending events to charger in case of unexpected behaviour. It is usually hit by a postman. We are continuously trying to implement features which would not require the Rest service in the long run.

 
