# Degrees

## A program that determines how many “degrees of separation” apart two actors are.

<img src="resources/degrees_output.png" width="1000">

According to the [Six Degrees of Kevin Bacon][kevin] game, anyone in the Hollywood film industry can be connected to Kevin Bacon within six steps, where each step consists of finding a film that two actors both starred in.

In this problem, we’re interested in finding the shortest path between any two actors by choosing a sequence of movies that connects them. For example, the shortest path between Jennifer Lawrence and Tom Hanks is 2: Jennifer Lawrence is connected to Kevin Bacon by both starring in “X-Men: First Class,” and Kevin Bacon is connected to Tom Hanks by both starring in “Apollo 13.”

We can frame this as a search problem: our states are people. Our actions are movies, which take us from one actor to another (it’s true that a movie could take us to multiple different actors, but that’s okay for this problem). Our initial state and goal state are defined by the two people we’re trying to connect. By using breadth-first search, we can find the shortest path from one actor to another.

## Implementation

Inside the `degrees` directory there are two sets of CSV data files: one set in the `large` directory and one set in the `small` directory. Each contains files with the same names, and the same structure, but `small` is a much smaller dataset for ease of testing and experimentation.

Each dataset consists of three CSV files. A CSV file is a way of organizing data in a text-based format: each row corresponds to one data entry, with commas in the row separating the values for that entry.

In `small/people.csv`, each person has a unique `id`, corresponding with their `id` in [IMDb][imdb]’s database. They also have a `name`, and a `birth` year.

Next, in `small/movies.csv`, each movie also has a unique `id`, in addition to a `title` and the `year` in which the movie was released.

Now, `small/stars.csv` establishes a relationship between the people in `people.csv` and the movies in `movies.csv`. Each row is a pair of a `person_id` value and `movie_id` value. The first row (ignoring the header), for example, states that the person with `id` 102 starred in the movie with `id` 104257. Checking that against `people.csv` and `movies.csv`, we’ll find that this line is saying that Kevin Bacon starred in the movie “A Few Good Men.”

Next, in `degrees.py`, at the top, several data structures are defined to store information from the CSV files. The `names` dictionary is a way to look up a person by their name: it maps names to a set of corresponding ids (because it’s possible that multiple actors have the same name). The `people` dictionary maps each person’s id to another dictionary with values for the person’s name, birth year, and the set of all the movies they have starred in. And the movies dictionary maps each movie’s id to another dictionary with values for that movie’s title, release year, and the set of all the movie’s stars. The `load_data` function loads data from the CSV files into these data structures.

The `main` function in this program first loads data into memory (the directory from which the data is loaded can be specified by a command-line argument). Then, the function prompts the user to type in two names. The `person_id_for_name` function retrieves the id for any person (and handles prompting the user to clarify, in the event that multiple people have the same name). The function then calls the `shortest_path` function to compute the shortest path between the two people, and prints out the path.

### Finding the shortest path between two actors

The `shortest_path` function returns the shortest path from the person with id `source` to the person with the id `target`.

* Assuming there is a path from the `source` to the `target`, this function returns a list, where each list item is the next `(movie_id, person_id)` pair in the path from the `source` to the `target`. Each pair is a tuple of two ids.

* For example, if the return value of shortest_path were `[(1, 2), (3, 4)]`, that would mean that the `source` starred in movie 1 with person 2, person 2 starred in movie 3 with person 4, and person 4 is the `target`.

* If there are multiple paths of minimum length from the `source` to the `target`, this function returns any of them.

* If there is no possible path between two actors, this function returns None.

* This function call the `neighbors_for_person` function, which accepts a person’s id as input, and returns a set of `(movie_id, person_id)` pairs for all people who starred in a movie with a given person.

In order to improve the efficiency of the search, this function checks for a goal as nodes are added to the frontier: if we detect a goal node, we don't add it to the frontier, we simply return the solution immediately.

The file `util.py` contains the class implementations for `Node`, `StackFrontier`, and `QueueFrontier`.

## Resources
* [Search - Lecture 0 - CS50's Introduction to Artificial Intelligence with Python 2020][cs50 lecture]

## Usage

**To find how many “degrees of separation” apart two actors are:** 

* Inside the `degrees` directory: `python degrees.py [csv]`

## Credits
[*Luis Sanchez*][linkedin] 2020.

A project from the course [CS50's Introduction to Artificial Intelligence with Python 2020][cs50 ai] from HarvardX.

[kevin]: https://en.wikipedia.org/wiki/Six_Degrees_of_Kevin_Bacon
[imdb]: https://www.imdb.com/
[cs50 lecture]: https://www.youtube.com/watch?v=D5aJNFWsWew&feature=youtu.be
[linkedin]: https://www.linkedin.com/in/luis-sanchez-13bb3b189/
[cs50 ai]: https://cs50.harvard.edu/ai/2020/
