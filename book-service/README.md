# Cancun Hotel - Book Service

## MENU
* 1 - Introduction
* 2 - Service Goals
* 3 - Service Description
* 4 - Service Configuration
* 5 - Exceptions Handling

# 1. Introduction
The Book Service from the Cancun Hotel is the service with the purpose to do a new booking or list all bookings for the user.

# 2. Service Goals
We expect from the service:
* Return all bookings for the user, independent on their current status;
* Do a new booking according to the users input;
* Handle exceptions properly and avoid explicit errors return.

# 3. Service Description
To fulfill it's purpose of return user's bookings, the service filters:
* Provide a GET service under the endpoint *'/book/{userId}/listAll'*;
* All books from the table *tb_book* where *user_id* equal parameter *userId*.

To fulfill it's purpose of do a new booking for the user, the service:
* Provide a POST service under the endpoint *'/book/'*;
* Expect a body for the request with:
    * roomId
    * userId
    * startDate
    * endDate
    * email
* Validade if all fields, except email, was filled;
* Validate if startDate is before or equal endDate;
* Validate if startDate and endDate has three days or less of difference;
* Validate if provided dates are available;
* After all confirmed, the new book is stored in the table *tb_book*;
* Send an email to the user with the booking configuration, if it was requested.

# 4. Service Configuration
The service was made using Spring Boot (for easier configuration and inbound server), Spring Web (for the service expose) and Spring Data (for database manipulation). It also has direct connection with the Eureka Server as it need to be part for supply the Gateway. The service is covered by Unit Tests (JUnit, Mockito and H2 Database).

Other technologies used on it was: swagger and lombok.

The server points by default to the port *8300* and looks for the Eureka Server under *http://localhost:8050/eureka/*.

# 5. Exceptions Handling 
If any required fields are not sent (roomId, userId, startDate and endDate), throw the exception IllegalArgumentException, return error *400* and message *'One or more parameters sent is wrong'*;

If the difference between the two dates are bigger than 3, throw the exception InvalidRangeDatesException, return error *404* and message *'It is not possible to book more than 3 days'*;

If the range of dates between *startDate* and *endDate* has at least one day not available, throw the exception NoAvailableDatesException, return error *404* and message *'One or more dates informed is not available'*;

If the *roomId* informed can't find any room, the service need throw the exception RoomNotFoundException, return error *404* and message *'Room not found for ID={id}'*.

If the *userId* informed can't find any user, the service need throw the exception UserNotFoundException, return error *404* and message *'User not found for ID={id}'*.

For any unexpected exception, service need return error *500* and message *'There was a internal error to complete the request'*.