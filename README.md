# vacation_climate_analysis
%matplotlib inline
from matplotlib import style
style.use('fivethirtyeight')
import matplotlib.pyplot as plt

import numpy as np
import pandas as pd

import datetime as dt

#Reflect Tables into SQLAlchemy ORM
# Python SQL toolkit and Object Relational Mapper
import sqlalchemy
from sqlalchemy.ext.automap import automap_base
from sqlalchemy.orm import Session
from sqlalchemy import create_engine, func

engine = create_engine("sqlite:////Users/price/PycharmProjects/berg/hawaii.sqlite")

# reflect an existing database into a new model
Base = automap_base()
# reflect the tables
Base.prepare(engine, reflect=True)
# We can view all of the classes that automap found
Base.classes.keys()

# Save references to each table
Measurement = Base.classes.measurement
Station = Base.classes.station

print(str(Measurement.__table__.columns))
print(str(Station.__table__.columns))

# Create our session (link) from Python to the DB
session = Session(engine)

precip = session.query(Measurement)
for p in precip:
    print(p.id) 

# Exploratory Climate Analysis

# Design a query to retrieve the last 12 months of precipitation data and plot the results

# Calculate the date 1 year ago from the last data point in the database


from datetime import datetime, timedelta
from datetime import datetime, timedelta
from sqlalchemy import func
start_date = session.query(Measurement, func.max(Measurement.date)).all()[0][1]

print(start_date)

from datetime import datetime

date_string = "2012-12-12 10:10:10"
start_date = datetime.fromisoformat(start_date)

filter_after = start_date - timedelta(days = 365)

# Perform a query to retrieve the data and precipitation scores
precip = session.query(Measurement).filter(Measurement.date >= filter_after).all()

len(precip)
frame_list = []
for p in precip:
    frame_list.append({'date' : p.date, 'precip' : p.prcp})
    
precip_frame = pd.DataFrame(frame_list)
print(precip_frame.shape)

precip_frame = precip_frame.sort_values('date', ascending=True)

print(precip_frame.head(20))
# # Save the query results as a Pandas DataFrame and set the index to the date column

# # Sort the dataframe by date

# # Use Pandas Plotting with Matplotlib to plot the data
import matplotlib.pyplot as plt
import datetime
import numpy as np

plt.plot(precip_frame['date'].values,precip_frame['precip'].values)
plt.show()

# Use Pandas to calcualte the summary statistics for the precipitation data

precip_frame['precip'].describe()

# Design a query to show how many stations are available in this dataset?
session.query(Measurement).distinct(Measurement.station).group_by(Measurement.station).count()

# What are the most active stations? (i.e. what stations have the most rows)?
# List the stations and the counts in descending order.
session.query(Measurement.station, func.count(Measurement.station)).group_by(Measurement.station).all()

 Using the station id from the previous query, calculate the lowest temperature recorded, 
# highest temperature recorded, and average temperature of the most active station?

