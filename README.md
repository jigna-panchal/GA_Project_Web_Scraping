# GA_Project_Web_Scraping
Web Scraping from Indeed.com and Predicting Salaries

To all my friends looking for a new job, this just may come in handy!

Over the last couple of weeks, I've been working on a project for the Data Science course I'm enrolled in. In light of the Mission Impossible 6 release, the following is a themed overview of my project!
![Mission Impossible Logo](https://vignette.wikia.nocookie.net/missionimpossible/images/f/ff/Mission-impossible-5103f256e2e3a.png/revision/latest?cb=20171119182212)   
(cue the music)    
__The Mission:__ (should we choose to accept it) Scrape all data science job posts from Indeed.com for a select number of cities, clean it up, and create a model to predict whether the salary of the posting is above or below the median.


__Part I: Scrape the Data__    
__*The Challenge:*__ Using Beautiful Soup, parse through the listing data scraped from Indeed.com for Data Science jobs.   
__*The Approach:*__ Using the requests library, I set up a request to the URL I was reviewing. I wanted to take a look at one page and review the information contained on that page before attempting to scrape hundreds of job postings. Once I set the request, I used Beautiful Soup to parse through the data. I started with looking at one row of data to understand the HTML tags being used, what information was available and any unique identifiers I could use to find that information. Beautiful Soup searches the html code by way of tags and attributes. This means that it can return you with the information you are looking for as long as you provide it with the correct path that contains all of the unique info.   

Once I reviewed the first row, I wrote 4 functions to extract the location, company, job title and salary from the html. In each of the four functions, the argument called for the soup variable, which was set to BeautifulSoup(request.text, 'html.parser'). I created an empty list for the attribute, then created a for loop where each attribute found in the html, with a specified path, would be appended to the list. If there was no such attribute for that posting, the list would be appended with 'NaN' and move on to the next. The function would ultimately provide you with a list of that specific attribute for all the postings. I was now ready to construct the big scrape!

The scrape called for a URL template, a number of max results per city, a list of cities to search, an empty results data frame and 5 empty lists - 4 for the attributes we are scraping and 1 for the search city. From there I started a for loop. For each city in my list of cities, for each result (up to the max number of results per city), go to the URL, initialize the request and soup variable and then append those results to the empty lists. Finally, append the results data frame with those lists. It took a while to scrape the data (I used max results of 500 per city), but it was all in my data frame!   
__Mission Accomplished?__ YES

__Part II: Cleaning the Data__    
__*The Challenge:*__ Although we got all of the data into a nice data frame, there was a lot to clean. For starters, salaries can be per year/month/hour, they have $ in front and some have a range.   
__*The Approach:*__ First, I converted all the non-yearly salaries to NaN by using a map function and checking for 'year'. Next, I dropped all of the non-yearly salary rows of data by subsetting the data frame and saving it as a new one. After that, I dropped all duplicate entries using a .drop_duplicates function. Finally, I needed to convert the salaries, which meant I needed to convert the string to an integer and if there was a range for the salary, calculate the mean. I created a function to look at each line in the salary column and strip the string of $, commas, and 'ayear'. Then, if its a range, which means there will be a '-', I split the numbers by the '-' and find the mean. Finally, I convert the strings to floats and map my salary column to my new, cleaned version.   
The final part was to save the cleaned data frame to a csv file.   
__Mission Accomplished?__ YES

__Part III: Predict if the Salary is above or below the Median__    
__*The Challenge:*__ Predict a binary variable, whether the salary is high or low, based on features extracted from the title.   
__*The Approach:*__ I created a classifier variable (binary: 1 for high and 0 for low) that would indicate whether the salary for that posting was higher or lower than the median of all job postings.   
***Note this does not take into consideration differences in salary due to cost of living. A better approach would be to classify based on the median of the city. That's will be for another day.***    
Next, I was to build a model based on just the location to predict salary class. For this, I dummified the city variable, which was the city I searched for each posting. Then I set out to build a model using Logistic Regression and a Decision Tree Classifier. For both, I set my X as the various cities and Y as the salary class. Next, created my train/test split, scaled the data, fit it to the model, obtained the salary class predictions on the test set, and obtained an accuracy score. The Logistic Regression (LR) produced an accuracy score of 0.7166, while the Decision Tree Classifier (DTC) produced 0.7833. In addition to the accuracy score, for the LR model, I plotted the coefficients in order of absolute value, which showed that the top 3 cities with the largest impact were San Francisco, New York, and Portland. For the DTC model, I created a data frame for the feature importance, which showed the top 3 as New York, Portland and Miami. As these are two different approaches, I would expect differences in the top 3 variables, but was surprised not to see San Francisco as important in the DTC model. 

The next segment called for building a model based on features extracted from the title. Here, I reviewed the titles in my data set to get a better understanding of what sort of words might have an indication to salary value (i.e. Director vs. Junior, Engineer vs Analyst, etc.). As such, I created a new column for each word I thought would have some influence on salary and if the title contained that word, it would have a 1, or else 0. I also grouped certain words together such as Manager and Mgr, Assistant and Asst, and Senior and Sr. 

Once my new features were set, I went through the same process as above and produced a Logistic Regression, Decision Tree Classifier, Logistic Regression CV with L1 penalty values, and a Logistic Regression CV with L2 penalty values. Here were the top 3 features for the LR and DTC models:   

__Logistic Regression:__  Research, Assistant, City of New York; Accuracy of 0.733    
__Decision Tree Classifier:__ Research, City of New York, Scientist; Accuracy of 0.766    

For the Regression CV with penalties, I got of 0.7833 and 0.7166 for L1 and L2 respectively. Overall, seems that the best model was made using the Logistic Regression CV with L1 penalty values.    
__Mission Accomplished?__ YES

__HOW IT ENDS__    
While I could have further tuned my models, extracted more features, I found that I made a pretty accurate model. I wanted to decrease the number of false positives (classifying as high salary when its lower than the median), therefore, I increased the class 1 probability threshold to 0.90, which means that if the probability of the observation being class 1 (higher than the median) is greater than 0.90, classify it as 1, else 0. This reduces the chances of people assuming a higher salary, when its below. 

Finally, I plotted an ROC curve with an area of 0.81 and a precision-recall graph with a score of 0.84. 

Hope that helps anyone trying to scrape data! Comment with questions!
