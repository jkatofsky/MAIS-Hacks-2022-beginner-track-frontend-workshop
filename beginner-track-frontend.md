# MAIS Hacks 2022 Beginner Track Frontend Workshop

A.K.A. a little web-dev crash course :) I assume *very minimal* web dev experience; some previous exposure to HTML and JavaScript will help, though. I'm going to go fast because we don't have a ton of time alloted, but will be making this document available to everybody and sticking around afterwards to help out!

Feel free to ask questions at any time!

**In this workshop, we will:**

1. Set up a basic webpage using HTML
2. Create a form on that page to capture user input
3. Make our page look pretty using a CSS library
4. Send the user input as an input for the API made in the beginner backend workshop (running locally on my laptop for demonstration purposes)
5. Briefly touch on hosting

The idea is to demonstrate the flow of making a basic frontend for an ML app, so that you will have some newfound tools to apply to your project!

<!-- TODO: update all code samples based on the actual file -->

## 1) Setup

At the end of the day, `.html` files are simply text files, so you can edit them any way that you like. However, it's often easier to use a text editor designed for web development. Arguably the most popular one (and what I will use in this workshop) is [Visual Studio Code](https://code.visualstudio.com/).

By convention, we name the HTML file for our website `index.html`, and fill it in with some boilerplate code:

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>MAIS Hacks 2022!</title>
    </head>
    <body>
    </body>
</html>
```

We can add some content to the page within the `<body>`:

```html
...
<body>
    <h1>MAIS Hacks 2022 Frontend Workshop</h1>
    <h2>Monthly Rent Price Predictor</h2>
</body>
...
```

And take a look at our page by opening it in the browser.

## 2) Creating a form

### The markup

We add some HTML form elements to the `<body>` to provide a place for the user to input the number of rooms and display the predicted price

```html
...
<body>
   ...
    <label>Enter the number of bedrooms in the house:</label>
    <input type="number" min="1" id="bedrooms">
    <button id="predict">Predict rent</button>
    <p>
        <span><b>Rent:</b></span>
        <span id="price"></span>
    </p>
</body>
...
```

### Connecting the form to JavaScript

We then use JavaScript to save references to the input, button, and prediction elements, and temporarily log the number of bedrooms to the console to make sure everything is working


```html
...
<body>
   ...
    <script>
        const predictButton = document.getElementById('predict');
        const bedroomInput = document.getElementById('bedrooms');
        const priceSpan = document.getElementById('price');

        predictButton.addEventListener('click', () => {
            const bedrooms = bedroomInput.value;
            console.log(bedrooms);
        })
    </script>
</body>
...
```

Later, we will use the value of `bedrooms` when we call the API.

## 3) Styling

But first, a little detour, because our site is looking pretty ugly at the moment. To keep the scope of this workshop reasonable, we won't be writing any CSS code directly. Instead, we will use a pre-made stylesheet. There are lots of options for this, and I'm personally a fan of [water.css](https://watercss.kognise.dev/). It doesn't require any changes to our HTML and automatically things will look pretty nice.

```html
...
<head>
    ...
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/water.css@2/out/dark.css">
</head>
...
```

Isn't that nicer?

## 4) Calling the API

Since this workshop is **strictly about frontend stuff**, we're using the API that was already made in the backend workshop. But we will still do a quick refresher of how it works at a high level.

### The API format

The API endpoint is of the following format:

- The endpoint is `http://127.0.0.1:5000/predict` and accepts `POST` requests.
- It expects `Content-Type: application/json`.
- The request body is then be of the format `{"bedrooms": 2}`
- The response will then look like `{ "prediction": 119.03267408357351 }`

### The fetching code

With this in mind, here is a new version of the `<script>` to call the API with the supplied `bedrooms`. I extracted the fetching logic into a function called `getPricePrediction` for readability. We will also make the button click callback `async` since `getPricePrediction` is async as well (since *it* calls the `fetch` function, which is itself `async`).

```html
...
<script>
    ...
    // using an API for this workshop...
    // but you can use whatever makes the most sense for your use case!
    const getPricePrediction = async (bedrooms) => {
        const response = await fetch(
            `http://127.0.0.1:5000/predict`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    bedrooms
                })
            });
        const responseJson = await response.json();
        return responseJson.prediction;
    }

    predictButton.addEventListener('click', async () => {
        const bedrooms = Number(bedroomInput.value);
        const pricePrediction = await getPricePrediction(bedrooms);
        priceSpan.innerHTML = pricePrediction;
    })
</script>
```

<!-- TODO: make better -->

Some possible adaptations are that...

- API formats vary widely, both in the expected request format and the expected response format.
- You don't have to have made the API yourself - there's lots of cool stuff out there.
- Instead of using an API, your project may have something AI-ey happening directly within the frontend, such as using Tensorflow.js or querying a premade set of model outputs.

## 5) Hosting the frontend

Not going to go in depth with this, but note that if you are deploying your frontend you will also want to deploy the backend somewhere! Since your users won't have the API running on their machines when they access your website.

That aside, it is super duper easy host a frontend do this with something like https://pages.github.com/. Just include your `index.html` in a repository and setup the Pages feature, and boom!
