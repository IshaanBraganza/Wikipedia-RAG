Name is misleading, the repository should rather be named wikipedia RAG.

To quickly summarise, this is a program I made on my raspberry pi 5 in which I tried multiple ways of making an RAG that takes the entirety of wikipedia as its source, and based on your question it currently finds the top 10 chunks which can then be fed into an AI which answers your question.

At its current state: 
There are multiple extra files, like all the FAISS files and qdrant files so the entire repo needs a nice clean, plus I still need to make the cosine similarity slightly better (maybe a different method), and I need to add the part where the chunks are fed into an AI, but if you still want to see that code check the repository called "file-search" in which you will (eventually somewhere) find the code that I will most likely use in this project when I decided to finish it and clean it up properly.
