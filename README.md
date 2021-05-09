## **Asynchronous Functions In JavaScript**

JavaScript Is a programming language commonly used In web development, and Is the basis for many websites and online tools. While JS was originally only meant for use In browser-based environments, there are now frameworks that allow the language to be used standalone, such as Node.JS, for example. One of the unique aspects of JS programming Is the ability to make functions asynchronous. 

In programming languages like Python, code Is executed In a linear fashion, following the exact flow a program was written with. JS, on the other hand, uses asynchronous functions to execute certain blocks of code while the rest of the program Is still running. This Is done by utilizing another feature of JS, known as promises. A promise Is an object that, upon being called, will return a placeholder value until a real value can be produced. If this object successfully produces a value, that means the promise has been "resolved", and the object's value will be updated. If there Is some sort of error In the process of producing a value, the promise will be "rejected", and (depending on how the program handles errors) can return debugging Information. With this Idea of promises established, you may begin to see how this relates to asynchronous functions. 

Let's take a look at some JS code (written In the Node.JS framework):
```
1 function dataPull(sku) {
2	var url = 'http://www.somesite.com/api/' + sku;
3	const response = fetch(url);
4	const pageText = response.text()
5	const pageJSON = JSON.parse(pageText);
```
This function will execute a GET request (a way of pulling Information from some URL using HTTP), parse the JSON data the request returns, and then store everything In a variable titled pageJSON. This function would work great In a linear-style programming language, but with JS you might run Into some Issues. 

Take a use case of web-scraping product data from a website using their API. Imagine you have a list of product SKUs you want to monitor, and will be making one request for each SKU every few seconds. You will want to make this code asynchronous so that It can pull data In the background while the rest of your script continues to run. While the script Is running, line 3 will Initialize the GET request, and line 4 will Immediately try and parse the text of that request. This will cause an error, since (In almost all cases) the script will not be able to retrieve the Information from the URL In the time that the next line of code Is executed. Here Is one possibly Implementation of asynchronous functions to help fix this.


```
1 async function dataPull(sku) {
2	var url = 'http://www.somesite.com/api/' + sku;
3
4	try {
5		const response = await fetch(url);
6		const pageText = response.text()
7		const pageJSON = JSON.parse(pageText);
8	} catch (err) {
9		console.error(err);
10	}
```
The first thing you will notice about this code Is the "async" Identifier In line 1. This tells JS that the function (when called) should run asynchronously. Next, you will see the use of "try" and "catch". This addition allows us to handle errors in the asynchronous function. The script will attempt to execute the code in the "try" block, and if any sort of error occurs, it will be "caught" and logged to the console. The next (and arguably most important) change Is the "await" keyword used In line 5. Adding await to a function call tells JS that the function will return a promise (In this case, the data of somesite.com) and that the script should not continue execution until the promise Is resolved or rejected. This solves the Issue of line 6 executing and throwing an error, since It has to wait for the promise of line 5 to be returned. 

So, how might this function look In a real world use case? 
```
(async() => {
	do {
	
		//Creates an array which will be filled with functions
		let urlArr = [];

		//Loop that Iterates through every SKU In a seperate file
		for (sku of skus) {
		
			//Selects a random proxy from a list
			let prox = proxies[Math.floor(Math.random() * proxies.length)].split(':');

			//Adds the dataPull function with a proxy and SKU to the array
			urlArr.push(dataPull(prox, sku));
	}
		//Executes all of the dataPull calls In the array, and uses await
		//to make sure the program retrieves all of the promises before
		//another loop Is called
		await Promise.all(urlArr);
		
	} while (status == "Good"); //Loops while there are no errors
})()
```
Here, we have an anonymous async function that pulls from a list of SKUs and proxies, builds an array where each element Is a call to the dataPull function, and then executes each element of the array and waits for all of the promises to be resolved or rejected before continuing. The actual structure of the function might seem confusing, so to visualize It you can look at this pseudocode:
```
asyncronous function {
	loop while no errors {
		for every SKU In a list {
			-select a random proxy
			-add the dataPull function to our array
		}
		
		-execute every element In our array and wait 
		 for the promises to be returned
	}
}
```

This Is just one example of the use cases of asynchronous functions. This shows both the benefits of using them (efficiency of tasks we want to run "In the background") and also some of the Inconveniences/difficulties of Implementing asynchronous functions that work smoothly (e.g. promise handling and proper error handling). While not every use case warrants an asynchronous function, JS gives you the option of using them to make your code run efficiently In the way you design.
