# Graphlab_recco_model
recommendation model based on graphlab
import pandas
import pandas as pd
import csv
import numpy
import graphlab
from os import path
import graphlab as gl
from datetime import datetime
userdata = ['user_id', 'user_name', 'interest']
user_data = pd.read_csv('F:/userdata.csv', names=userdata, encoding='latin-1')
user_data = graphlab.SFrame(user_data)
user_data
ratingdata = ['user_id', 'user_name', 'interest', 'time', 'event_name', 'event_id', 'rating']
rating_data = pd.read_csv('F:/Newuserrating.csv', names=ratingdata, encoding='latin-1')
rating_data = graphlab.SFrame(rating_data)
rating_data
eventdata= ['interest', 'time', 'city', 'state', 'url', 'group_name', 'event_name', 'event_id']
event_data = pd.read_csv('F:/11-25-torontoevents.csv', names=eventdata, encoding='latin-1')
event_data = graphlab.SFrame(event_data)
event_data
train_data, test_data = rating_data.random_split(.9, seed=5)
len(train_data), len(test_data)
model = gl.recommender.create(train_data, user_id='user_id', item_id='event_id',target='rating',user_data=user_data,item_data=event_data)
model
results = model.recommend(users=None,k=5)
sf=event_data.select_columns(['event_id', 'event_name','interest', 'time'])
results=results.join(sf,on='event_id').sort(['user_id','rank'])
results
import csv
f= open("F:/recommendation1.csv",'wb')
csvwriter=csv.writer(f)
count=0
for row in results:
    if count==0:
        header=['user_id','event_id','rank' ,'event_name','interest', 'time']
        csvwriter.writerow(header)
        count+=1
    write=row['user_id'],row['event_id'], row['rank'], row['event_name'],row['interest'], row['time']
    csvwriter.writerow(write)
f.close()
