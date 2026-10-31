A self made RAG (Retrieval-Augmented Generation) pipeline made using python that has the entirety of Wikipedia (as of June 1st) as its data.

__TLDR:__

Takes the base .xml.bz2 Wikipedia dumps and converts them into 84 files in total, 42 .jsonl files which contain all the paragraph chunks in word format, and 42 .npy files which contain all the embeddings, then uses cosine similarity to get the top 10 chunks, which are then fed into an Ollama model to give an output which answers the question. Estimated time per response on an Apple M4 Pro with 24GB of RAM: 1m 5s (_Without the AI output and just giving the 10 chunks_)

__Full Explanation:__

1. Processes each .xml.bz2 dump individually and gets the raw text of all pages, except redirect pages. For each article, it makes a separate jsonl entry, and for each dump file it makes a new jsonl file. That process was done in the case of if any errors came up in the code, then it will be much easier, and less time consuming, to restart from a specific dump file and keep all the previous files, than to restart from the beginning.
2. After the dumps are converted into jsonl files, it then filters through each article and removes the following articles types:
("talk", "user", "user talk", "wikipedia", "wikipedia talk", 
    "file", "file talk", "mediawiki", "mediawiki talk", 
    "template", "template talk", "help", "help talk", 
    "category", "category talk", "portal", "portal talk", 
    "draft", "draft talk", "module", "module talk",
    "gadget", "gadget talk"). The thought behind this was obvious: all of these articles did not actually contain any useful information that would be queried, like "wikipedia talk" and other "-talk" articles. This significantly reduced file sizes, which will be an issue that will be explained later.
3. After only the articles useful to the pipeline has been filtered, it then chunks all the articles by iterating through every word and every 256 words it creates a new chunk, and every 500,000 chunks it creates a new jsonl file, which is important for embedding later and for saving progress just in case something fails.
4. This step is the most time consuming as here it uses the sentence_transformer library to convert every chunk into .npy vector embeddings. Resource capabilities on the device this was originally made on was the reason it was 256 words per chunk and 500,000 chunks per file. After sentence_transformer does its job, it makes a .npy file, which corresponds to a .jsonl file. So embedding_1.npy has the embeddings of chunks_1.jsonl. This is useful later for querying the database and getting the text chunks.
5. The user now can query the data base! First the user enters the query, which turns into a vector embedding using sentence transformer, which then is used in cosine similarity in order to find the top 10 most relavent chunks to the users question. From there, the chunks then get passes into an Ollama model (a decently large one is needed as context size needs to be large, somewhere around 8B models might be good, but the larger the model the more relavent chunks you can pass) which then gives a text output that aims to answer the user query, along with certain parameters (like format of the answer) given by the user.

__Notes:__

- In total, all 82 files take up ~63.49 GB of space, and if you are going to be recreating this project it is highly recommended that after each step in the pipeline folder, the files used in the previous step are deleted, for example after the filterarticles.py script is ran, delete all the .xml.bz2 files. The reason I'm saying this is because when I kept all the files, the total storage the entire project took as a whole exceeded 200 GB.
- When I first ran everything in the pipeline for converting the Wikipedia dumps into chunks and embeddings, I was doing it on a Raspberry PI 5 with 8 GB RAM, and some of it on my M2 Air with 8 GB RAM. When I reached the embedding stage of the project I had no choice but to do all the embedding on Google Colab instead of either the Macbook or the PI. This was because I had originally estimated somewhere between 15 days to 60 days of estimated, continuous, run time, but after switching to Colab I had finished all the embeddings in around a week.
- Keep in mind that if you want to use this code on your own set of Wikipedia files, almost all the directories need to be set.

__In The Future:__

As of now, I am working towards adding the Ollama functionality, using tkinter as the main display window, and making a single file that the user can run to do everything from querying the database, to making it. I ideally want to turn this into a working app, but I know I can only do that if I manage to reduce the size of the chunks and the embeddings by a minimum of 30 GB (Half the current size).
