# Yelp-visualization
DSC106 Project 3 by Angelina Zhang, Ziyu Huang, and Bella Wang

---

## Rational for our design decisions 🥇
  Our aim is to clearly show people where businesses they should go to based on ratings and the number of reviews from the Yelp dataset. We chose Santa Barbara as the target city. First, we filtered the data by state and city. Then we look at the rating and number of reviews for each business to give users who want to find out the best business to go to in the city suggestions and choices. For design decisions, we chose circle markers to represent each business location, with a size channel based on the number of reviews (more reviews mean larger in size). It allows users to quickly grasp the popularity of each business. We also use color encoding to represent the ratings of each business: 5-star ratings represented in green, 4-star in yellow, 3-star in orange, 2-star in dark orange, and 1-star in red, offering users a quick visual indication of each establishment's reputation, providing users with an intuitive understanding of the reputation of each business. The two positional makers, longitude and latitude, give ideas of where the business is located. When the map is moved, we ensure the points remain correctly positioned relative to the map viewport. Additionally, when users hover over a circle with the mouse, we will highlight the circle and display text for each business, including the name, number of reviews, and categories. This will help users identify which store they are looking at on a large-scale map and provide better insight into each business in Santa Barbara. We have implemented a slider feature to provide users with a clear visualization of the reputation levels of businesses. This slider allows customers to easily identify businesses based on their reputation. For instance, if a customer is specifically looking for businesses with a reputation of 4 stars or higher with more than 500 reviews, they can conveniently navigate to these optimal places by adjusting the sliders for review and star accordingly. This intuitive functionality empowers users to quickly find high-quality establishments, enhancing their overall experience. We considered clustering all data into several cycles, illustrating the density of the business, and showing all details when zooming in at the very beginning. However, clustering all businesses together will affect customers to find places with high ratings. Instead, we added a search box below the slider. The sliders make it more convenient to search for targeted businesses for the users. The search box allows users to input text and returns all stores whose names contain that string pattern (not case-sensitive). Additionally, it will display the top five stores with the most reviews. If the user finds the desired result, they can click on it from the top five stores, and the map will automatically locate that store for them. 

---

## Contributions 👏
  We worked together first to make a final decision about what question we wanted to answer and what part of the dataset we needed to look into. Then we split the work by separating the coding for channels and discussing how to make improvements after the first draft came out. Angelina wrote the first draft of the code for the website with positional, color, and text channels (also transformed data in the dataset), and the first draft of the writeup, also contributed to the improvements of visualization by adding interactive text (name and categories of each business). For me, I spent seven hours, and the most time-consuming parts were importing and transforming data on D3. Bella added the change in the volume of  business based on the star each place gets, helped to write the readme file and helped to deploy the visualization on the github. Tony constructed two sliders to split the business on the star they got and the amount of reviews, created the text boxes for helping the users looking for a specific store, and designed the visualization when the user hovered over a circle. 
