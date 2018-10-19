# Project-1: Automatically display NJTRANSIT train departure times.
#### Author: Kieyn Parks
#### Language: Python
#### Database: MongoDB (NoSQL)

**About the author:**

I had been working for the past 15 years in various technical roles, mostly in the Telecommuncations field as an Engineering tech. Sensing a change in the economy towards computers, software and data, I felt the need to get retraining.  So, in the summer 2016 just before entering Southern New Hampshire University (SNHU) I started brushing up on the C/C++ programming language and having fun while doing it. I first enrolled as a data science undergraduate but quickly transitioned to computer science where I remained for the duration of my studies.  

Studying computer science at SNHU was difficult at times but I persevered. I especially enjoyed the coding class C++, Java, python and the database classes MySQL, MongoDB. The Java and python classes focused more on applications, object-oriented programming and testing. The C++ class focused on algorithms and data structures. To round off my software engineering skills I have taken classes in Software design and Engineering, Operating systems, Artificial intelligence and Machine learning to name a few. Also I have just recently enrolled in an introductory course for autonomous vehicles from Udacity.  

### A Brief Project History:

**The Idea!** 

The Project in the ePortfolio is one I wanted to get done for a long time. The idea is a simple one. I would open the application and get the departure times for the next trains in either direction; this will all happen automatically without any interaction from the user and would be the top use case for the application. 

**How I arrived at this idea?** 

The idea came about as one of necessity. I don’t take the train often so when I do it’s usually a last-minute decision. Using the application provided by the carrier, NJTRANSIT, is slow. The NJTRANSIT application and all other third-party applications require the user to enter departing station and destination station, some would even ask you for the time you want to leave for a more narrowed list of departures. I found the User experience with these applications to be too cumbersome when all I want is information for the next few departure times. 

**A few things to note.**  

- Departure times are all the same every day Monday to Sunday every 6 months.
- There are two schedules Weekday schedule and Weekend Schedule. 
- There are only a few transit stations and main terminals. 
- There are 6 train lines each with unique stops sometimes the stops overlap. 

**______________________________________________________________________________________________________________________________**

This project is a combination the following:
- Engineering and Design
- Database
- Data Structures and Algorithms 


## Project Overview:

This project is in two parts. The first part is the database managment system (DBMS) for a NoSQL database and the second part is the user interface (UI). The DBMS impliments CRUD functionallity for Inserting, deleting, update, and searching of the database. The U.I. interfaces with the user and displays information to the screen.

## User Interface discription:

This application is designed to return the train schedule for New Jersey Transit train system from the database to the user. The goal is that the entire process happens automatically using the GPS location of the user. Once the application is opened the GPS location is grabed from the user's device. The GPS coordinates is used to determine the closest train station to the user with the next 4 departure times going in each direction; this is the top use case for this application. The second use case will be trip planning.

Trip planning is a bit more complicated as it requres the user to input some information, nameley trip starting point and trip ending point no train staion name is neccessary just the town name and the program will workout closest stations possible including all connections to complete the trip.

## Code Review:
Below is a vedio of the code review:

## [Code Review](https://www.youtube.com/embed/S5SBJUDnSNw)


## Design & Engineering:
#### Flow Chart: use case 1.
![](flow_chart.png)
#### Use Case Diagram:
![](use_case.png)

### [LINK - Design and Engineering Narrative](https://github.com/CodeSenpii/CodeSenpii.github.io/blob/master/Design_Engineering.docx)

## Algorithm and Data Structure:
Python offers sevral data structures.
- List
- Sets
- Associative Array/ Dictionary / Maps
- Tuples

The main data structure used in this programs is a **list of tuples** This contains the the name of the stations, the station IDs and the latitude and Longitude coordinates of each train station. Train station names and location hardly ever changes so keeping a data structure with this information embedded in the software save on processing time and database queries.

**Example of the list of tuples.**
```
jersey_coast_line2 = [('Aberdeen Matawan Station', 'AM',  [40.420161,-74.223702]), ('Allenhurst Station','AH', [40.237659,-74.006769]), ('Asbury Park Station',
'AP', [40.215359,-74.014782]),  ('Avenel Station','AV', [40.577620,-74.277530]),('Bay Head Station','BH', [40.077178,-74.046201]),
('Belmar Station','BS', [40.180590,-74.027301]),  ('Bradley Beach Station','BB', [40.203753,-74.018891]), ('Elberon Station','EL',[40.265292,-73.997617]),
('Elizabeth Station','EZ', [40.667859,-74.215171]), ('Hazlet Station','HZ', [40.415385,-74.190393]), ('Little Silver Station','LS', [40.326715,-74.041054]),
('Manasquan Station','SQ', [40.120573,-74.047685]), ('Middletown New Jersey Station','MI', [40.389780,-74.116131]), ('Monmouth Park Station','MK', [40.313427,-74.015172]),
('New York Penn Station','NY', [40.750051,-73.992358]), ('Newark Penn Station','NP', [40.734223,-74.164550]), ('Perth Amboy Station','PE', [40.509398,-74.273752]),
('Point Pleasant Beach Station','PP', [40.092718,-74.048191]), ('Red Bank Station','RB', [40.348284,-74.074535]), ('Secaucus Junction Station','SE', [40.761190,-74.075821]),
('South Amboy Station','CH', [40.48431,-74.28014]), ('Spring Lake Station','LA', [40.150559,-74.035481]), ('Linden Station','LI', [40.629487,-74.251772]),
('Long Branch Station','LB', [40.297145,-73.988331]), ('Woodbridge Station','WB', [40.55661,-74.27775]), ('EWR Newark Airport Station','NA', [40.704417,-74.190714])]
```
Another data structure used in this project is **Python Dictionary.**
This dictionary is used to convert 2 digit military time (type: "text") to  military (type: Integer)

**Example of python Dictionary.**
```
time_map_local = {'00':0, '01':1, '02':2, '03':3, '04':4, '05':5, '06':6, '07':7, '08':8,
                  '09':9, '10':10, '11':11, '12':12, '13':13, '14':14, '15':15, '16':16, '17':17,
                  '18':18, '19':19, '20':20, '21':21, '22':22, '23':23}

```
## USER INTERFACE APPLICATION
```
'''
 This program will get the closest tain station to the user and the depature time of the next train
Haversine Fomula - is used To calculate great-circle distance using latitude and longitude in decimal format
This formula does not take into account the non-spheriodal shape of the earth and may overshoote
or undershoote in certain circumstances but is a good "AS-THE-CROW-FLIES" short distance estimator (+- 0.5%) which 
should still be usefull for our purposes.

'''

from pymongo import MongoClient
import json
from bson import json_util
import math
import time

#the database is "market" and the collection is "stocks"
connection = MongoClient('localhost', 27017)
db = connection['njtransit']
collection = db['schedule']

R = 3961 # radius of the earth in miles

# this is the facilitate the conversion of 24H military time to conventional time


''' The jersey coast train line array. the layout is station name followed 
by station coordinates
'''

jersey_coast_line1 = ['Aberdeen Matawan Station', 'AM',  [40.420161,-74.223702], 'Allenhurst Station','AH', [40.237659,-74.006769], 'Asbury Park Station',
'AP', [40.215359,-74.014782],  'Avenel Station','AV', [40.577620,-74.277530],  'Bay Head Station','BH', [40.077178,-74.046201],
'Belmar Station','BS', [40.180590,-74.027301],  'Bradley Beach Station','BB', [40.203753,-74.018891], 'Elberon Station','EL',[40.265292,-73.997617],
'Elizabeth Station','EZ', [40.667859,-74.215171], 'Hazlet Station','HZ', [40.415385,-74.190393],  'Little Silver Station','LS', [40.326715,-74.041054],
'Manasquan Station','SQ', [40.120573,-74.047685], 'Middletown New Jersey Station','MI', [40.389780,-74.116131], 'Monmouth Park Station','MK', [40.313427,-74.015172],
'New York Penn Station','NY', [40.750051,-73.992358], 'Newark Penn Station','NP', [40.734223,-74.164550], 'Perth Amboy Station','PE', [40.509398,-74.273752],
'Point Pleasant Beach Station','PP', [40.092718,-74.048191], 'Red Bank Station','RB', [40.348284,-74.074535], 'Secaucus Junction Station','SE', [40.761190,-74.075821],
'South Amboy Station','CH', [40.48431,-74.28014], 'Spring Lake Station','LA', [40.150559,-74.035481], 'Linden Station','LI', [40.629487,-74.251772],
'Long Branch Station','LB', [40.297145,-73.988331], 'Woodbridge Station','WB', [40.55661,-74.27775], 'EWR Newark Airport Station','NA', [40.704417,-74.190714]]

jersey_coast_line2 = [('Aberdeen Matawan Station', 'AM',  [40.420161,-74.223702]), ('Allenhurst Station','AH', [40.237659,-74.006769]), ('Asbury Park Station',
'AP', [40.215359,-74.014782]),  ('Avenel Station','AV', [40.577620,-74.277530]),('Bay Head Station','BH', [40.077178,-74.046201]),
('Belmar Station','BS', [40.180590,-74.027301]),  ('Bradley Beach Station','BB', [40.203753,-74.018891]), ('Elberon Station','EL',[40.265292,-73.997617]),
('Elizabeth Station','EZ', [40.667859,-74.215171]), ('Hazlet Station','HZ', [40.415385,-74.190393]), ('Little Silver Station','LS', [40.326715,-74.041054]),
('Manasquan Station','SQ', [40.120573,-74.047685]), ('Middletown New Jersey Station','MI', [40.389780,-74.116131]), ('Monmouth Park Station','MK', [40.313427,-74.015172]),
('New York Penn Station','NY', [40.750051,-73.992358]), ('Newark Penn Station','NP', [40.734223,-74.164550]), ('Perth Amboy Station','PE', [40.509398,-74.273752]),
('Point Pleasant Beach Station','PP', [40.092718,-74.048191]), ('Red Bank Station','RB', [40.348284,-74.074535]), ('Secaucus Junction Station','SE', [40.761190,-74.075821]),
('South Amboy Station','CH', [40.48431,-74.28014]), ('Spring Lake Station','LA', [40.150559,-74.035481]), ('Linden Station','LI', [40.629487,-74.251772]),
('Long Branch Station','LB', [40.297145,-73.988331]), ('Woodbridge Station','WB', [40.55661,-74.27775]), ('EWR Newark Airport Station','NA', [40.704417,-74.190714])]

time_map_local = {'00':0, '01':1, '02':2, '03':3, '04':4, '05':5, '06':6, '07':7, '08':8,
                  '09':9, '10':10, '11':11, '12':12, '13':13, '14':14, '15':15, '16':16, '17':17,
                  '18':18, '19':19, '20':20, '21':21, '22':22, '23':23}
    
time_map_departure_am = {"12":0,"1":1, "2":2, "3":3, "4":4, "5":5, "6":6, "7":7, "8":8,
                         "9":9, "10":10, "11":11}
    
time_map_departure_pm = {"12":12, "1":13, "2":14, "15":15, "4":16, "5":17, "6":18,
                         "7":19, "8":20, "9":21, "10":22, "11":23}

# calculating distance
def calculateDistance2(lat1, lon1):
    coords = []
    station_name = ''
    
    distance_information = []    
    
    
    a = 0.0
    c = 0.0
    d = 0.0
    
    for sName, sId, sCoords in jersey_coast_line2:
        
        coords = sCoords
        station_name = sName
        station_id = sId
            
            
        # print(coords)
        dlon = math.radians(lon1 - coords[1]) # - lon1 
        dlat = math.radians(lat1 - coords[0]) #- lat1 
            
        rlat = math.radians(coords[0])
            
            
        a = (math.sin(dlat/2))**2 + math.cos(math.radians(lat1)) * math.cos(math.radians(coords[0])) * (math.sin(dlon/2))**2 
        c = 2 * math.atan2( math.sqrt(a), math.sqrt(1-a) ) 
        d = math.ceil(R * c)  # distance (where R is the radius of the Earth)
            
        distance_information.append((station_name, station_id, d)) # convert to information sets
                   
    return distance_information

def calculateDistance(lat1, lon1):
    coords = []
    station = ''
    
    distance_information = []    
    
    
    a = 0.0
    c = 0.0
    d = 0.0
    
    for index in range(len(jersey_coast_line1)):
        
        if index % 3 == 0:
            coords = jersey_coast_line1[index+2]
            station = jersey_coast_line1[index]
            station_id = jersey_coast_line1[index+1]
            
            
           # print(coords)
            dlon = math.radians(lon1 - coords[1]) # - lon1 
            dlat = math.radians(lat1 - coords[0]) #- lat1 
            
            rlat = math.radians(coords[0])
            
            
            a = (math.sin(dlat/2))**2 + math.cos(math.radians(lat1)) * math.cos(math.radians(coords[0])) * (math.sin(dlon/2))**2 
            c = 2 * math.atan2( math.sqrt(a), math.sqrt(1-a) ) 
            d = math.ceil(R * c)  # (where R is the radius of the Earth)
            
            distance_information.append((station, station_id, d)) # convert to information sets
                     
            
        else:
            continue
            
    return distance_information
            
            
def closestStation(info):
    closest = ''
    dist = 0
    station = ''
    station_id = ''
    
    for name, sid, distance in info:
        
        if dist == 0:
            dist = distance
            station = name
            station_id = sid
        else:
            if dist > distance:
                dist = distance
                station = name
                station_id = sid
                
    print(station, dist, station_id)
        
    return station_id

    
def findDocument(value):
    try:
        key_value = {"station_id" : value}        
        doc = collection.find_one(key_value)
        return doc
       
    except:
        print('Error occured')
        
        
def getDepartureTimesNB(document):
    
    ''' 
    * This method attemps to deal with the inconsistencies in the time formats. The time formats in the database
    * is of the form "1:23am" or "T:2:34pm" where T is a special symbal that indicates additional information about this train 
    * the user needs to be notified of. The device to is in military form "00:20" in ordr to compare the times the units must 
    * be standardized.
    '''  
    PM = False
    AM = False
    tHour = ''
    tMin = ''
    departure_times_nb = []
    
    local_time_h = 0 # hour time on device
    north_bound = document["departure_time"]["north_bound"] 
  
    #parse local time
    date = time.asctime()      
    day, month, day_of_month, ttime, year = date.split()
    hour, minute, seconds = ttime.split(':')
    
    # millitary time to standard time
    if hour == '00':
        AM = True
        local_time_h = time_map_local[hour]   #  set to 0?     
    elif int(hour) <= 11:
        AM = True
        local_time_h =time_map_local[hour]
          
    else: 
        PM = True
        local_time_h = time_map_local[hour] # convert  military time to statndard time
        print('hour:', local_time_h)
    
    # parse departure times
    for dp in north_bound:       
        
        if AM == True:
            if 'am' in dp:
                remove_am_from_time = dp[0:-2]
                dTime = remove_am_from_time.split(':')
                #This needs to be looked at ****************what if hour is 12 handle seperatle
                
                if time_map_departure_am[dTime[-2]] == 0:
                    dTime_h = 0
                else:
                    dTime_h = time_map_departure_am[dTime[-2]] # depature time hour                     
         
                dTime_m = dTime[-1] # departure time minute
                                
                try:
                    dTime_special_notice = dTime[-3] # some times may have a special value in index 0
                except:
                    pass
                
                if local_time_h <= dTime_h and (int(minute) < int(dTime_m)):
                    departure_times_nb.append(dp)
                
        elif PM == True:
            if 'pm' in dp:
                remove_pm_from_time = dp[0:-2]
                dTime = remove_pm_from_time.split(':') # departure time list
                
                dTime_h = time_map_departure_pm[dTime[-2]] # depature time hour 
                dTime_m = dTime[-1] # departure time minute
                
                print(dTime_h, dTime_m)
                
                try:
                    dTime_special_notice = dTime[-3] # some times may have a special value in index 0
                except:
                    pass
                        
                
                # This needs to be looked at ******************* what is time ism 12
                if local_time_h <= dTime_h and int(minute) < int(dTime_m):
                    
                    departure_times_nb.append(dp)
                
       
       # if int(mh) == int(dTime[0]) and int(minute) < int(dTime[1]):
            
            #departure_times_nb.append(dp)
    return departure_times_nb

# south bound depature times
def getDepartureTimesSB(document):
    
    ''' 
    * This method attemps to address  the inconsistencies in the time formats. The time formats in the database
    * is of the form "1:23am" or "T:2:34pm" where T is a special symbal that indicates additional information about this train 
    * the user needs to be notified of. The device to is in military form "00:20" in ordr to compare the times the units must 
    * be standardized.
    '''
   
    PM = False
    AM = False
    tHour = ''
    tMin = ''
    departure_times_sb = []
    
    local_time_h = 0 # hour time on device
     
    south_bound = document["departure_time"]["south_bound"] 
    
    #parse local time
    date = time.asctime()
        
    day, month, day_of_month, ttime, year = date.split()
    hour, minute, seconds = ttime.split(':')
   
    # millitary time to standard time
    if hour == '00':
        AM = True
        local_time_h = time_map_local[hour]   #  set to 0?     
    elif int(hour) <= 11:
        AM = True
        local_time_h =time_map_local[hour]     
    else: 
        PM = True
        local_time_h = time_map_local[hour] # convert  military time to statndard time
        print('hour:', local_time_h)
    
    # parse departure times
    for dp in south_bound:       
        
        if AM == True:
            if 'am' in dp:
                remove_am_from_time = dp[0:-2]
                dTime = remove_am_from_time.split(':')
                #This needs to be looked at ****************what if hour is 12 handle seperatle
             
                if time_map_departure_am[dTime[-2]] == 0:
                    dTime_h = 0
                else:
                    dTime_h = time_map_departure_am[dTime[-2]] # depature time hour                     
               
                dTime_m = dTime[-1] # departure time minute
                                    
                try:
                    dTime_special_notice = dTime[-3] # some times may have a special value in index 0
                except:
                    pass
                
                if local_time_h <= dTime_h and (int(minute) < int(dTime_m)):
                    departure_times_sb.append(dp)
               
                
        elif PM == True:
            if 'pm' in dp:
                remove_pm_from_time = dp[0:-2]
                dTime = remove_pm_from_time.split(':') # departure time list
                
                dTime_h = time_map_departure_pm[dTime[-2]] # depature time hour 
                dTime_m = dTime[-1] # departure time minute
                
                print(dTime_h, dTime_m)
                
                try:
                    dTime_special_notice = dTime[-3] # some times may have a special value in index 0
                except:
                    pass
                     
                # This needs to be looked at ******************* what is time ism 12
                if local_time_h <= dTime_h and int(minute) < int(dTime_m):
                    
                    departure_times_sb.append(dp)
                
       
       # if int(mh) == int(dTime[0]) and int(minute) < int(dTime[1]):
            
            #departure_times_nb.append(dp)
    return departure_times_sb
                
def main():
    distance_info = [] 
    document = {}
    departure_timesNB = []
    departure_timesSB = []
    
                     
    lat = 40.297000   #float(input("Enter latitude: "))
    long = -73.988000  #float(input("Enter longitude: "))
    
    # get distance info
    distance_info =  calculateDistance2(lat, long)
    
    # get the station id for the closest station
    station_id = closestStation(distance_info)
       
    # query database for document matching the station
    document = findDocument(station_id)
        
    # extract departure times from document
    departure_timesNB =  getDepartureTimesNB(document) # north bound
    departure_timesSB =  getDepartureTimesSB(document) # south bound
    
    
    print("Next departure time NB 1: ", departure_timesNB[0])
    print("Next departure time NB 2: ", departure_timesNB[1])
    print("Next departure time NB 3: ", departure_timesNB[2])
    print("Next departure time NB 4: ", departure_timesNB[3])
    
    print("Next departure time SB 1: ", departure_timesSB[0])
    print("Next departure time SB 2: ", departure_timesSB[1])
    print("Next departure time SB 3: ", departure_timesSB[2])
    print("Next departure time SB 4: ", departure_timesSB[3])
   
    connection.close() # close database connection
   
if __name__ == '__main__':
           main()

```
### [LINK - Algorithm and Data Structure Narrative](https://github.com/CodeSenpii/CodeSenpii.github.io/blob/master/Narrative_ADS.docx)

## Database:
The database used in this project is a MongoDB NoSQL. A Database managment system application written in python is used to Insert, Update, Delete and Search the database (njtransit).
## NJTRANSIT DATABASE MANAGMENT SYSTEM
```
#------------------------------------------------------------------------------------
'''
*  This program demonstrates the use of CRUD functions that insert, update and delete
*  a document on the Mongodb NoSql database
'''
#-----------------------------------------------------------------------------------

from pymongo import MongoClient
import json
from bson import json_util
import datetime
from collections import OrderedDict

#the database is "market" and the collection is "stocks"
connection = MongoClient('localhost', 27017)
db = connection['njtransit']
collection = db['schedule']

fields = ["line", "station_name", "station_id", "coordinates", "departure_time"]

# collect the data from the user
def createDocumnet():
    
    print("****INSERT DOCUMENT****")
    values = [] # hold the values for the key:value pairs
    value = ""
    lat = 0.0   # laitude
    long = 0.0   # longitude
        
    #get the keys from the key array 
        
    for key in fields:
        # read  the values from user keyboard and append to values array
        # all values captured as strings
        if key != 'coordinates' and key != 'departure_time':                                
            value = input('Enter ' + key + ': ') 
            values.append(value)
        elif key == 'coordinates':
            lat = input('Enter latitude' + ': ') 
            long = input('Enter longitude' + ': ')                
        elif key == "departure_time":
            times_nb = ""
            times_sb = ""
            print("\nEnter North-Bound or South-Bound times when prompted **leave a space between times**")
            print("\n*******************IMPORTANT NOTICE: DATA ENTRY FORMAT*******************")
            print("\nData Entry Example: 9:13am 9:20am 10:30am... hit -ENTER- key when done!")
            print("\n********************************END**************************************")
                
            times_nb = input("\nEnter **NORTH-BOUND** times! Press ENTER key to submit:")
            times_sb = input("Enter **SOUTH-BOUND** times! Press ENTER key to submit:")
                
            print("This is what you entered!")
            print("North-Bound: " + times_nb, "\nSouth-Bound: " + times_sb)
                
            # TODO get a confirmation that the info is good or bad
                
            times_nb = times_nb.split(' ')
            times_sb = times_sb.split(' ')
            
    document = {}
    document["line"] = values[0]
    document["station_name"] = values[1]
    document["station_id"] = values[2]
    document["coordinates"] = {"latitude" : lat, "longitude" : long}
    document["departure_time"] = {"north_bound" : times_nb, "south_bound" : times_sb}
            
    return document        
    
#*******************insert a document ******************************
def insertDocument(document):
    try:
        result = collection.insert_one(document)
        print("Data insert successfull")
    except ValidationError as ve:
        abort(400, str(ve))
        print("Data insert fail")
    return result # return TODO

#delete a document
def deleteDocument():
    print("WARNING! You are about to delete a document!")
    station_id = input("Enter station id: ")
    doc_delete = {"station_id" : station_id}
    try:
        doc = collection.delete_one(doc_delete)
        if doc.delete_count > 0:
            print("document deleted")
        else:
            print("No documents deleted!")
                
    except InvalidOperation as iv:
        print("ERROR: " + iv)
    pass

# update docments in the collection
def updateDocument():
    station_id = input("Enter station id: ")
    search = {"station_id" : station_id}
    
    field = input("Enetr field to update: ")
    value = input("Enter value: ")
    
    newValue = {"$set" : {field : value}}
    
    try:
        update = collection.update_one(search, newValue)
        print("update completed")
        
    except:
        print("update failed!")
        
# Search database for station
def searchDocument():        
    exit = False
    while not exit:
        
        try:
            menu = "1) Find by station name. \n2) Find by station id."
            print(menu)
            option = int(input("Option: "))
            if option == 1:
                station = input('Enter station name: ')
                doc = collection.find_one({"station_name" : station})
                exit = True
                return doc
            elif option == 2:
                station_id = input('Enter station id: ')
                doc = collection.find_one({"station_id" : station_id})
                exit = True
                return doc
            else:
                doc = "No Search String Entered!"                
                return doc
        except:
            
            print('Error occured\n')
            pass

# program starts here
def main():
    #menu options
    menu = "Choose and operation. \n1) Insert Documents. \n2) Search Documents.\n3) Delete a Document."
    print(menu)
    option = "0"
    
    #parse option input should be integer only
    while option.isdigit():
        try:
            option = int(input("Choose Option (1-4): "))
            break;
        except:
            print("Invalid selection!")

    #creating the document   
    if option == 1:
        document = createDocumnet()
        #print(insertDocument(document))
        print(document)
            
    elif option == 2:
        document = searchDocument()
        print(document)
        
    elif option == 3:
        deleteDocument()
        
    elif option == 4:
        updateDocument()       
    else:
        pass
                
if __name__ == '__main__':
           main()

```
### [LINK - Database Narrative](https://github.com/CodeSenpii/CodeSenpii.github.io/blob/master/DBS.docx)




### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/CodeSenpii/CodeSenpii.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
