The PRG Pattern or Post/Redirect/Get is an important web development design patter to be aware of, especially when it comes to working with databases. 

The main point of it is to stop undesirable side effects occurring after submitting a POST request to a server/database. 

Imagine you were shopping online and you submitted a request to place an order. Without redirecting upon making the POST request the page may refresh and submit a second order. Implementing the PRG Pattern will help to avoid this.

It should be pointed out that the problem will not be entirely eliminated. For example, if a user refreshes the page as the POST request is being made there may still be a duplicate order. However PRG is still useful and something you should always try to include in your app design.

## Without PRG

As you can see below this is an example of how the app could work without using the PRG Pattern.

Duplicate POST requests may not happen every time but by not redirecting to another page you run the risk and for what? It's fairly simple to implement a redirect and enhances the user experience.

![PRG Incorrect](https://i.imgur.com/6WPH7AK.png)

## With PRG

Using the PRG pattern should not change the user experience much as it they should not see anything different with all the work happening server side.

After a user successfully submits a POST request, a 300 response (likely 308) should be returned along with the user being redirected to a new page. This should trigger a GET request and return a 200 response along with confirmation of successfully posting to the database.

This means that should the user now refresh the page, to get a status update for example, the only request that will be duplicated is a GET request.

![PRG Correct](https://i.imgur.com/KflfjF6.png)

