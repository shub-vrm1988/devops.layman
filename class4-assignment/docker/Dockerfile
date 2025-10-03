FROM python

#Here you will create a directory & move to py-app directory
WORKDIR /py-app 

#Here you copy all the files from you local directory to the working directory
COPY . . 

#Here you will install all the dependencies mentioned in requirements.txt file
RUN pip install -r requirements.txt

#This is a default port number for flask application. 
#f i don't expose the port, my application will not be accessible outside the container(e.g from a broser)
EXPOSE 5000

#We specifying this command to run the application when images runs as a container
CMD ["python","app.py"]