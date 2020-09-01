The PRG Pattern or Post/Redirect/Get is an important web development design patter to be aware of, especially when it comes to working with databases. 

The main point of it is to stop undesirable side effects occurring after submitting a POST request to a server/database. Imagine you were shopping online and you submitted a request to place an order. Without redirecting upon making the POST request the page may refresh and submit a second order. Implementing the PRG Pattern will avoid this.

## Without PRG

![PRG Incorrect](https://i.imgur.com/6WPH7AK.png)

## With PRG

![PRG Correct](https://i.imgur.com/KflfjF6.png)

