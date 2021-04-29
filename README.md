# Car90_GYR
Educational Project -- Data on Car make/models in 1990, created algorithm to sort based on value

Understanding the Project and Defining Value:
When approaching this project, I first considered both the explicit and implicit tasks assigned. I was to build a model, yes, but—perhaps just as important as the final 
output—was the way I internalized and handled the task’s instructions in the context of the Senior Analyst interview process. Given this, I wanted to see what I could 
accomplish in a vacuum without expert help when it came to general coding issues or developing the model’s algorithm; however, it should be stated that if this was a client 
deliverable, I’d of course work intimately with subject matter experts where applicable. Furthermore, as I imagine this task was partly designed to determine how candidates 
approach a given dataset, I didn’t see a need to find corroborative or substitutive data sources (as they would’ve increased the data available, but would have no marked 
impact on the skills needed to analyze said data). Having made these initial determinations, I was able to move on to the explicit task at hand. I wanted to put myself in 
the shoes of your average American (who likely knows very little about cars) so that I might develop an appropriate methodology that determines the various aspects of a 
car’s performance ability. While there are a host of reasons people buy cars, I wanted to focus on performance, opposed to those metrics which are more subjective in nature 
(comfort, design, country of origin—see: Country-of-Origin effect, cultural popularity, etc.), as this would allow me to create a widely applicable metric, regardless of 
individual variation in the consumer. With this in mind (and given a loose understanding of the variables presented in the dataset), I defined our categories of performance 
as: fuel efficiency, power-to-weight ratio (acceleration), top speed, handling, and reliability. As these relate to every potential performance aspect of the driving experience 
(minus braking, which I would have loved to include, but wasn’t confident I could come up with an appropriate model given the lack of relevant data), I was comfortable 
finalizing these as my guiding metrics and moving forward onto the research phase.

Research:
In beginning the work on my model, I faced two substantial issues: (1) my knowledge of cars was limited to the instrument cluster on my own jeep, and (2) my knowledge of 
Python was limited to the concepts covered in various training modules/online resources. In regard to the former, it was a nonquestion as to how to resolve this: with my 
performance metrics in mind, I used numerous online resources to determine what each field in the car90 dataset represented in the first place, which then allowed me to 
determine how they related to each respective performance metric (compiling my notes in the “Field Research” spreadsheet which you’ll find included on BaseCamp). As for 
the latter issue, I could certainly have used Excel to develop a model with more immediate efficiency (and, all things equal, would have done so if the project window hadn’t 
afforded me enough time to develop a Python model), however, if this had been a real project, I assumed that this analysis would not occur in a vacuum and that we’d likely 
need to apply our methodology on other similar datasets, so I chose to develop a model where someone could simply input the file name and receive the desired results. Python 
was the platform du jour for me in this respect, as—of all the languages I could reasonably employ based on my skillset at the start of the task—it offered the most streamlined 
approach. More so, when finished I could then independently test my model by replicating it in Excel, ensuring alignment in code and concept. Fortunately, the most difficult 
aspect of learning Python was—as is often the case in life—the first few steps. Once I was able to understand the basic infrastructure of how to get Python set up, determining 
the methods and functions needed to create my model was just one big, fun puzzle.

Performance Model Development:
The first step in my model development was determining what method I would use in deriving my performance algorithm. Normal Distribution and Z-Scores seemed like an apt 
approach, as—conceptually—value is just relative multivariable positioning. With this understanding, I developed my algorithm as follows:

  Performance = Fuel Efficiency Z-Score + Reliability Z-Score + Power to Weight Ratio Z-Score + Top Speed Z-Score + Handling Z-Score
  Without expert input on best practices to determine “performance”, I decided equal weighting would work fine. This kept the high-level methodology simple to understand from 
  a consumer perspective as well. As for determining each component of the Performance score, the methodology varied as follows:

  Fuel Efficiency = Mileage ZS (if value present) – (Composite Gear Ratio Score ZS + Engine Displacement ZS)/2
  For Fuel Efficiency, unfortunately the Mileage field was missing for ~52% of vehicles, so—to effectively use this a performance metric—I needed to find a way to offset the 
  missing data. I determined that high Gear Ratios and high Engine Displacement led to greater fuel consumption, so I used a normalized average of the two (with the Gear Ratio 
  component itself being a normalized average as two ratios were offered, reliant upon transmission type) in place of Mileage where it was missing, and used a normalized average 
  of Mileage and the Gear/Disp score when both were available. Admittedly, this is the one score I thought could potentially be improved from a methodology perspective. Using 
  the Pearson method of correlation and as a common-sense test, I found that the derived Fuel Efficiency had a correlation coefficient of .594 with the Type of car (a strong, 
  if not explicitly defined reflection of fuel efficiency), so I do think there’s opportunity to refine this score. One potential method might be to determine the relative 
  correlation of displacement and gear ratio (respectively) to mileage, and weigh each of their inputs in the Fuel Efficiency function according to those coefficients. At the 
  end of the day, though, I received results that were clearly indicative of a car’s true fuel efficiency and was satisfied with such.

  Top Speed = Horsepower ZS (weighted by Type ZS)
  For Top Speed, I found that a car’s maximum velocity is most strongly related to the net HP, with aerodynamics needing to be considered as well. Given this, I assigned a score 
  of 1-6 for the least to most aerodynamic car types and normalized those results. I then added those results to the normalized horsepower of each car to find the top speed. As 
  a note, I initially planned to use the car dimension fields in place of Type, but found that the two strongly correlated, so took the simpler approach of just normalizing Type

  Handling = (Tire ZS – Composite Steering Ratio ZS – Turning ZS)/3
  For Handling, I wanted to consider the car’s tires, steering ratio and turning radius. For tires, a large diameter, wide width, and small sidewall all directly translate to 
  better handling, while the turning radius can inform agility, and the steering ratio determines response time to movements from the driver.  As we’re defining Handling ourselves,
  I didn’t find any need to weigh one component metric over another, but—similar to Fuel Efficiency—would certainly be interested in an expert perspective to help optimize the 
  algorithm.

  Reliability = Reliability ZS
  For Reliability, I simply assigned a score of 1-5 for the least to most reliable cars and then normalized that distribution.
  
  Power-to-Weight Ratio = (HorsePower/Weight) ZS
  For the Power to Weight Ratio, I divided each car’s horsepower by its weight to determine acceleration potential and then normalized that distribution. As can likely be 
  ascertained, acceleration and top speed are closely linked and strongly correlated, but they do categorically represent different aspects of a car’s performance. Top speed 
  relates to overall maximum HP output, while the power-to-weight ratio reflects the rate at which the engine can get the car moving, each—in a way—bookending a general “ability 
  to move in a straight line” metric (if you will).

Final Steps:
After developing the performance model, my final step was to determine the actual “Value” score by subtracting the normalized price from the normalized performance score. I 
chose this as the final derivation of our Value indicator because—given the vacuum we’re working within—a car market might be said to be in equilibrium (or fairly priced) when 
the price z-score equals the performance z-score. So, we’re in essence determining what degree—when measured against the entire market—a car is under or overvalued for a 
consumer. Understandably, this is only valid for vehicles with pricing information in our dataset. In the case of those cars without a price, these are automatically ranked as 
red regardless of performance score, as making any purchase without knowing the price first derails the entire point of consumerism and thus is categorically irrelevant.

Post-Analysis Checks:
Following my analysis, I wanted to confirm that the results made sense from a common-sense perspective and that they could be replicated, proving that everything functioned as 
intended. In regard to the first test, I took the top 10, middle 10 and bottom 10 cars and researched each online. I quickly found that our results seemed to make perfect sense,
with the top results reflecting high-performing non-luxury vehicles and the bottom results reflecting low performing vehicles with a value prop of comfort. 
The model replication as well was fairly pain free, with my main issue arriving when I found that the individual Z-Scores for each base field in the car90 dataset were lining 
up between the Python and Excel models, but their overall performance scores were not. After digging into each platforms’ model, I eventually determined that it was rooted in 
the values that I had derived from the “Tires” field of the dataset. Upon further troubleshooting, I found that all of the normalization and aggregation formulas seemed to be 
working just fine, so I then realized it must be an issue with the data itself. Sure enough, I discovered that my formula in excel for extracting a tire’s aspect ratio had 
appropriately derived the ratio for all vehicles except those which had an aspect ratio of 100 (or, within the confines of our data, those which solely featured the tire width).
After troubleshooting out the discrepancy, my excel model produced the exact same results as my Python model, offering independent validation and closing the book on this project.  

Takeaways:
While I’ve addressed a few of the ways I’d approach this project if it wasn’t in the vacuum of the interview process, one aspect of vehicle ratings I’d certainly like—and even 
insist—we incorporate into our model is safety. Assuming the output of this project would be intended for consumers in a manner akin to Bluebook’s current product (i.e. 
directive in nature), we’d be doing a disservice to the public to not include a separate safety rating. While people’s desire for safety in vehicles is certainly subjective, 
the vehicle’s actual safety is not, so—without a safety rating—we’re omitting key decision-making information from our results that arguably invalidates the ontology of the 
product itself.

