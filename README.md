# Diamond_Vancouver_Clinic

Main part of this program is the file “MainV4.c “, and the file “Main.h” contains variables and definitions.

The overall scenario is as follows. At the beginning of the day, people arrive to lobby, and elevators take them to their desired clinics at upper floors. After elevators are done with their duty on their way up, they go back to lobby to serve more people and, on their way down, they take people waiting at floors (to go to lobby) to lobby as well. Thus, whenever people are done with their appointment at their corresponding floor, they will go to lobby by either one of the elevators that are going down, or by an elevator from a lower floor (than that person) that we will assign to them.

The code consists of 3 main parts, each taking care of one of the 3 main types of events.

1)	PERSON_ARRIVES_LOBBY
This part of the code takes care of the lobby arrival events. Whenever a person arrives at lobby and an elevator is available at the lobby, we assign the person to an available idle elevator at lobby. After elevator loads 4 people (where 4 is the elevator capacity), we despatch the elevator to the upper floors. Once the elevator offloads the last person and is not planned to go to another upper floor (by means of the variable “elevator[index].elevator_going_to=0” that will be described more below), we define an “ELEVATOR_ARRIVAL” event, after which the elevator will change its direction to return to the lobby. Please note that on its way up, if the elevator is planned to go to an upper floor (after it offloads its last passenger), then the elevator will go to that upper floor to load a person who wants to return to lobby.

2)	ELEVATOR_ARRIVAL
This is the largest chunk of the main code. Elevator arrival is defined when an elevator arrives at a floor. ELEVATOR_ARRIVAL can happen in both directions.
a.	UP: If an elevator is going up, the elevator arrival event will be defined only once, and that is when the elevator is done with its duty on the way up. We define elevators duty in the way up to offload all its passengers, and if required, to go to an upper floor to take a person to the lobby. The structure of the code is such that when the elevator starts going up, it knows (by using the variable “elevator[index].elevator_going_to”) whether or not it is supposed to go to an upper floor (after it offloads all the passengers on the way up).
b.	DOWN: After elevator’s mission is done on the way up, it changes direction to go to the lobby. We let this procedure happen floor by floor, and we define a new ELEVATOR_ARRIVAL event at each floor on the elevator’s way down. The elevator checks on each floor on its way down, and if there is any person at floors waiting to go down, the elevator can take those people to the lobby as well (until its capacity is full). There are 3 possibilities here:
i.	If there are no people at a floor, then only an ELEVATOR_ARRIVAL event occurs, but there is no physical stop (i.e., no DOOR_TIME).
ii.	If there are people, actual stops happen (DOOR_TIME), and elevator loads people.
iii.	If the elevator’s capacity is full but there are people waiting at a floor, the elevator still stops physically (DOOR_TIME), but it does not load a new person.
3)	CLINIC _DEPARTURE
Whenever a person arrives at their appointment, we define a CLINIC_DEPARTURE event for a time when the appointment is done.  We assume that if there is an elevator in an upper floor (than this person who wishes to go down), then that elevator will take care of this person. This is consistent with the way we described the ELEVATOR_ARRIVAL event above. If there is no elevator in an upper floor, then we assign one of the other elevators to take care of this person. The following describes how we do this.
We calculate the time to reach to this person for all elevators, and then choose the one that takes the lowest time. We then assign that elevator to the floor at which this person is waiting at, by setting the variable “elevator[index].elevator_going_to” to that person’s floor. Please note that if it happens that another elevator takes this person sooner, we then re-set “elevator[index].elevator_going_to” to 0.
