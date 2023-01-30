# Sequelize Intro
Continue working with the Clark County Landscaping database, this time from an admin perspective.

## Summary
In this lab, you’ll continue using the database we set up during the demo for Clark County Construction. You’ll be writing in a new app though, this time one that’s for admin.

## Setup
1. Download the exercise materials from Frodo.

2. Navigate to the folder in your terminal.

3. Run `npm install` to get the project’s dependencies installed

4. Open your code and begin!

## Instructions
### Step 1: Dotenv
For our server files to work, we’re going to need dotenv.

1. Install ‘dotenv’ using `npm install dotenv`

2. Create a file at the root of your project called `.env`

3. In that file, create a `SERVER_PORT` variable and set it to 8765 (it needs to be this number or you’ll have to change all the front end code)

4. Additionally, create a `CONNECTION_STRING` variable and set it to your URI from bit.io (same as the one you used in the demo)

5. At the top of `controller.js`, require in the `dotenv` package and call the `config` method (you can ignore the other code in there until later steps)

6. Optional: destructure `CONNECTION_STRING` from `process.env` in `controller.js`

### Step 2: Sequelize
In order to make queries to our database, we’ll need Sequelize.

1. Install Sequelize and its dependencies using `npm install sequelize pg pg-hstore`

2. In `controller.js`, require the `sequelize` package and save it to a variable called Sequelize

3. Initialize a new Sequelize instance, passing in your connection string and an object that looks like this:

```
{
  dialect: 'postgres',
  dialectOptions: {
      ssl: {
          rejectUnauthorized: false
      }
  }
}
```

### Step 3: Getting Client Information
During the demo, we seeded our databases with lots of information. Now that we’re working on the admin side of things, we want a way to see all of our clients and their contact info. The front end is set up to receive an array of client info that contains data from both the `cc_users` and `cc_clients` tables, so we’ll need to use `join`.

1. In `controller.js`’s export object, write a new function called `getAllClients` (make sure it accepts `req` & `res`)

2. Using `sequelize.query` query your database for all the columns in both `cc_users` and `cc_clients` joining them where the `user_id` column matches

3. Handle the promise with `.then()` passing in a callback: `dbRes => res.status(200).send(dbRes[0])` (you can also add a `.catch`)

4. In `index.js`, comment line 20 back in (this line: `app.get('/clients', getAllClients)`)

5. Run `nodemon` (make sure you’re in the right directory, also you can keep it running for the duration of the lab)

6. Open `clients.html` (public folder) in your browser

7. You should be seeing your client information!

### Step 4: Pending Appointments
If you head to the home page of CCL Admin Portal, you’ll see sections for Pending, Upcoming, and Past Appointments. Pending appointments are those that have been requested but not yet approved. Let’s set up a query for those now.

1. In `controller.js`, write a new function called `getPendingAppointments`

2. Using `sequelize.query` query your database for all appointments that are not approved (approved = false) and order them by date with the most recent dates at the top.

3. Handle the promise with `.then() passing in a callback: ``dbRes => res.status(200).send(dbRes[0])` (you can also add a `.catch`)

4. In `index.js`, comment line 23 and 24 back in (these lines: `app.get('/pending'`, `getPendingAppointments`), and `app.get('/upcoming', getUpcomingAppointments)`)

5. In `home.js` (public folder), comment line 107 back in (this line: `getPendingAppointments()`)

6. Navigate to `home.html` in your browser

7. You should be seeing pending appointments!

### Step 5: Past Appointments
Next, you’ll be writing a query that’s similar to the triple join query from `getUpcomingAppointments`, reference it as you write yours. This one will get all of the past appointments.

1. In `controller.js`, write a new function called `getPastAppointments`

2. Using `sequelize.query` query your database for the following columns from their respective tables **cc_appointments**: appt_id, date, service_type, notes. cc_users: first_name, last_name. Reference the `getUpcomingAppointments` function to see how to join all the information together (you’ll need all the same tables again). Make sure to select only rows where both the `approved` and `completed` values are true. And order the results by date with the most recent at the top.

3. Handle the promise with `.then()` passing in a callback: `dbRes => res.status(200).send(dbRes[0])` (you can also add a `.catch`)

4. In `index.js`, comment line 25 back in (this line: `app.get('/appt', getPastAppointments)`)

5. In `home.js` (public folder), comment lines 108 and 109 back in (these lines: `getUpcomingAppointments()` and `getPastAppointments()`)

6. Navigate to `home.html` in your browser

7. Now you should be able to see all the past appointments as well as the pending ones.

### Step 6: Approving Appointments
Part of this function has been given to you because it’s taking care of auto-assigning employees to appointments for us. Finish it up with one query and the ‘Mark Approved’ buttons on the front end should work.

1. In the `approveAppointment` function, delete `*****YOUR CODE HERE****` and replace it with a query that updates the appointments table. It should set `approved` to equal true where the `appt_id` matches the one coming from the `req.body`, which has been destructured for you as apptId.

2. In `index.js`, comment line 26 back in (this line: `app.put('/approve', approveAppointment)`)

3. Navigate to `home.html` in your browser

4. Test out the ‘Mark Approved’ button!

**No appointments to approve?** There should be some appointments waiting for you from the demo. If there are not, start up your demo app again and request a few. Since they run on different ports, you can actually have your demo app and your lab app running at the same time.

### Step 7: Completing Appointments
Last but not least, let’s make the ‘Mark Completed’ button work.

1. In `controller.js`, write a new function called `completeAppointment`

2. Using `sequelize.query` query your database to update the appointments table. It should set `completed` to equal true where the `appt_id` matches the one coming from the `req.body`. (You can destructure `apptId` like in the previous step or use `req.body.apptId.`)

3. Handle the promise with `.then()` passing in a callback: `dbRes => res.status(200).send(dbRes[0])` (you can also add a `.catch`)

4. In `index.js`, comment line 27 back in (this line: `app.put('/complete', completeAppointment)`)

5. Navigate to `home.html` in your browser

6. Your whole page should work now! Once pending appointments are approved, they should move to the upcoming section, and once those are complete they should move to the past section.

**Push your code to GitHub!**

## Intermediate
Read about subqueries below and then complete the given problems using the postgres sandbox. Save your answers in a new file called `subqueries.sql`, which you can create in the lab exercise folder you’ve been working in.

### Subqueries: 
- Subqueries are a queries that rely on an outer query.

- A subquery is used to return data that will be used in the main query as a condition to further restrict the data to be retrieved.

### Problems
Open up the postgres sandbox to complete these problems. Save your answers in a file `subqueries.sql`. Push to GitHub when you’re done.

1. Get all invoices where the **_unit_price_** on the **_invoice_line_** is greater than $0.99.

2. Get all playlist tracks where the playlist name is Music.

3. Get all track names for **_playlist_id_** 5.

4. Get all tracks where the **_genre_** is Comedy.

5. Get all tracks where the **_album_** is Fireball.

6. Get all tracks for the artist Queen ( 2 nested subqueries ).
