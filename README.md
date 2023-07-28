# llama2.c optimizations

- Performance benchmarks (micro) and optimizations.
- (WIP) Zig and wasm port.

![llama2_c_ss_20230724_225531](https://github.com/cedrickchee/llama2.c/assets/145605/0b7de882-4c1a-4eda-8541-000c68dcfcf1)

## benchmarks

On my Linux laptop (Ice Lake/Core i7 processor) this runs at ~150 tokens/s. The fastest throughput I saw so far on Linux laptop:

With `zig cc` compiler and 15M parameter model:

```sh
$ zig cc -O3 -ffast-math -o runz4 run.c
achieved tok/s: 176.917761
```

With `gcc` compiler and 15M parameter model:

```sh
$ gcc -O3 -Ofast -funsafe-math-optimizations -o run4 run.c -lm
achieved tok/s: 171.890412
```

With `zig cc` compiler and 44M parameter model:

```sh
$ zig cc -O3 -Ofast -ffast-math -o runz11 run.c -lm -target x86_64-linux-musl
achieved tok/s: 46.642981
```

With parallelism and 15M parameter model:

```sh
$ gcc -Ofast -ffast-math -fopenmp -march=native -mtune=native -o run15 run.c -lm
$ ./run15 out/model.bin
achieved tok/s: 200.626959

# set number of threads
$ OMP_NUM_THREADS=4 ./run3 out/model.bin
achieved tok/s: 320.400501
```

With parallelism and 44M parameter model:

```sh
$ gcc -Ofast -ffast-math -fopenmp -o run3 run.c -lm
$ ./run3 out44m/model44m.bin
achieved tok/s: 47.969270

# set number of threads
$ OMP_NUM_THREADS=4 ./run3 out44m/model44m.bin
achieved tok/s: 69.797560
```

With parallelism, [mmap patch](https://github.com/karpathy/llama2.c/pull/30), and 15M parameter model:

```sh
$ gcc -Ofast -ffast-math -fopenmp -o run20 run.c -lm
$ OMP_NUM_THREADS=4 ./run20 out/model.bin
achieved tok/s: 287.964004
```

With parallelism, [mmap patch](https://github.com/karpathy/llama2.c/pull/30), and 44M parameter model:

```sh
$ gcc -Ofast -ffast-math -fopenmp -o run20 run.c -lm
$ ./run20 out44m/model44m.bin
achieved tok/s: 49.679798
```

See [performance](#performance) for compile flags that can significantly speed this up.

### Runs

**Compiler: zig cc**

**Model: 15M parameter**

Run 1:

```sh
$ zig cc -o run_zig run.c
$ ./run_zig out/model.bin
 Once upon a time, there was a little girl named Lily. Lily loved to play hide and seek with her friends. One day, Lily went to find her friends to hide. She looked high and low, but she couldn't find them anywhere. 
She asked her mom, "Mommy, do you permit me to hide so I can pretend to be the number zero?" 
Her mom said, "Yes, Lily, you can hide in the living room and I'll look for you. But be careful not to wander into the room." 
Lily agreed and ran to the living room. She saw her friends hiding behind the couch, but they were not in the corner. Suddenly, they heard a loud noise. They turned around and saw the curtain moving. 
One of her friends giggled and said, "Surprise! Lily, you're the first one to hide behind the curtain!" 
Lily giggled back and said, "You're the best at hiding, but I'm still the best at the sounds!" 
From that day on, Lily's friends played peek-a-boo with b
achieved tok/s: 9.488158
```

Run 2:

```sh
$ zig cc -O3 -o runz2 run.c
$ ./runz2 out/model.bin
 Once upon a time, there was a man who had a big axe. He used his axe to cut wood for his family and friends. One day, he went to the forest and saw a deer. The deer looked very serious and the man wanted to be friends with it. The man put down his axe and the deer stopped running. From that day on, the man and the deer were the best of friends. They would go on adventures together and share their food. The end.
<s>
 Once upon a time, there was a little boy named Timmy. Timmy was very shy and didn't like to talk to people he didn't know. One day, Timmy went to the store with his mommy and saw a big bag of cookies. Timmy wanted to buy the cookies, but his mommy said no because they were too expensive.
Timmy started to cry and said, "I want cookies, mommy!" His mommy said, "I know you want cookies, but they are too many and we don't want to buy them." Timmy didn't understand why they couldn't buy the cookies, so he asked his mommy.
His mommy explained,
achieved tok/s: 53.067993
```

Run 3:

```sh
$ zig cc -O3 -Ofast -o runz3 run.c
$ ./runz3 out/model.bin
 One day, a fat cat named Tom went to play with his friends. Tom saw a big tree and wanted to climb it. He got ready to go up, but his friend Sam the dog said, "No, it's not safe!"
Tom was not happy. He really wanted to climb the tree and play with his friends. So, Tom decided to quit trying to climb the tree. He went back to play with his friends instead.
While playing, Tom saw a big bird sitting on a branch. The bird was sad because Tom had quit playing too. The bird said, "I'm sorry, I didn't know you didn't want to climb the tree!" Tom thought about it and had an idea. He asked the bird, "Can you please let me go on my lawn and play with my friends?" The bird agreed and Tom was very happy. From that day on, Tom and the bird played together every day.
<s>
 Once upon a time, there was a cute puppy named Max. Max loved to play fetch with his owner, Lily. Lily would throw a ball and Max would chase after it. One day, Max found a bone in the park. He picked
achieved tok/s: 53.534086
```

Run 4:

```sh
$ zig cc -O3 -ffast-math -o runz4 run.c
$ ./runz4 out/model.bin
 Once upon a time, there was a little boy named Timmy. Timmy loved going to the park to play on the swings and slide. One day, Timmy saw a wise old owl sitting on a tree branch. 
"Hello, Mr. Owl," said Timmy. 
"Hello, Timmy," replied the owl. "What are you doing up there?" 
"I'm just looking at this tree," said Timmy. 
"Well, been here to get some food for me before it rained," said the owl. 
"I love nature," said Timmy. 
"Why do you belong here in the rain?" asked the owl. 
"I love the sun and the rain," replied Timmy. 
The owl smiled. "I'm glad you like it here. Now, let's sit under the tree and read a book together." 
And so, Timmy and the wise old owl sat under the tree, enjoying the cold rain and the beauty of nature.
<s>
 One day, a boy named Tom went to the woods with his dad. They wanted to find a rabbit to take home. The sky
achieved tok/s: 176.917761
```

Run 5:

```sh
$ zig cc -O3 -funsafe-math-optimizations -o runz5 run.c
$ ./runz5 out/model.bin
 Once upon a time, there was a little duck named Ducky. Ducky loved to play in the pond all day long. One day, Ducky saw a big puddle and wanted to play in it. But when Ducky got closer, it started to rain. The rain was coming down hard and the puddle got bigger and bigger. Ducky got scared and started to splash in the puddle.
Suddenly, a kind frog named Froggy came to help Ducky. Froggy helped Ducky get up and they both started to jump in the puddle. They splashed and played until they were tired. Then, they saw a big tree and decided to rest under it. Ducky was very happy to have a place to rest and the frog was happy to have a new friend. From that day on, they played together every day and had lots of fun. The end.
<s>
 Once upon a time, there was a little bird. The bird had many feathers. It liked to run and play in the big tree. One day, the bird saw a big fire. It was very hot and bright. The bird knew it had to help.

achieved tok/s: 161.412358
```

Run 6:

```sh
$ zig cc -Ofast -o runz6 run.c
$ ./runz6 out/model.bin
 Once upon a time, there was a little boy named Timmy. He had a toy car that he loved to play with. One day, he went to the park with his mom and saw a big dog. The dog was very friendly, but Timmy's toy car was spoiled with a scratch when he played with it. 
"Look, Mommy! The dog has a hurt do Snowy," said Timmy. 
"It's okay, Timmy. Snowy has a patch on her knee. She needs some love," replied Timmy's mom. 
Timmy took out his toy car and started to play with it again. His mom watched him and smiled. 
"This is a fun toy that can heal Snowy's pain," said his mom. 
Timmy nodded and continued to enjoy playing with his toy car. He knew that even if something got spoiled, Snowy would still be his friend to him.
<s>
 Once upon a time, there was a little girl named Lily. She loved the color purple and had a purple dress. One day, Lily went to the beach to surf with her friends. 

achieved tok/s: 53.668763
```

Run 7:

```sh
$ zig cc -O3 -march=native -o runz7 run.c
$ ./runz7 out/model.bin
 Once upon a time, there was a little boy named Timmy. Timmy loved to play with his soft toys in his bedroom. One day, his mom told him not to touch the stairs because they are dangerous. Timmy didn't understand why they were dangerous, so he asked his mom why they were dangerous.
He thought for a moment and then he started to imagine what would happen if he climbed up the stairs with his toys inside. Suddenly, he found himself at the top of the stairs! He started to climb up and down, and he was so happy that he could see everything from up close.
When he got to the top, he saw a big, beautiful window. He could see all the pretty flowers and trees outside. Timmy was so excited he wanted to stay there forever. But then, he remembered what his mom had said and decided not to go back downstairs. He hugged his soft toys and fell asleep, feeling very happy.
<s>
 Once upon a time, there was a little ant named Andy. Andy loved to march around the garden all day long. One day, he met a caterpillar named Sammy.
"Hi there,
achieved tok/s: 53.211391
```

Run 8:

```sh
$ zig cc -O3 -Ofast -ffast-math -o runz8 run.c
$ ./runz8 out/model.bin
 One day, a big parade was in town. Many people came to see the parade. There were big balloons, loud music, and fun toys. Everyone was happy to watch the parade.
A little boy named Tim was very excited. He clapped his hands together and started to march. His mom joined him, too. They marched and danced in the parade. The people in the parade clapped louder and louder. Tim and his mom laughed and clapped.
At the end of the parade, everyone in the town said, "Thank you, Tim, for the fun and the music!" Tim was happy that he could share the incredible parade with everyone. He waved goodbye to his mom and dad as the parade disappeared.
<s>
 Once upon a time, there was a little boy named Tim. Tim had a toy whip that he loved to play with. One day, Tim went to the park to play with his whip. He would run and whistle as he played. He did not see thing that was under a big rock. Tim was careless with his whip.
A big dog came and heard Tim barking.
achieved tok/s: 176.187199
```

Run 9:

```sh
$ zig cc -O3 -Ofast -ffast-math -o runz9 run.c -lm
$ ./runz9 out/model.bin
 Once upon a time, there was a little girl named Lily. She loved to play outside with her dog, Max. One day, while they were playing, Max accidentally broke the handle of his leash. Lily got very sad and didn't know what to do.
Her mom saw her crying and asked what happened. Lily told her about Max and how he broke the handle. Her mom said, "Don't worry, we can restore it." Lily was very happy to hear that and asked her mom how they could fix it.
Her mom took the broken handle and went to the store to buy some tools. While they were there, they saw a dependable man selling balloons. Lily asked her mom if she could buy one, and her mom said yes. Lily was very happy and said, "Thank you, Mommy! You are so nice."
<s>
 One day, a little boy named Tim found a bean. It was a big, brown bean. Tim felt very lucky. He wanted to send the bean to his friend, Sam. So, he went to Sam's house.
"Sam, look at my bean!" said Tim. "I get to send it to
achieved tok/s: 176.308540
```

**Compiler: zig cc**

**Model: 44M parameter**

Run 1:

```sh
$ ./runz4 out44m/model44m.bin
achieved tok/s: 46.223988
```

Run 2:

```sh
$ ./runz2 out44m/model44m.bin
achieved tok/s: 16.032064
```

Run 3:

```sh
$ ./runz5 out44m/model44m.bin
achieved tok/s: 44.774814
```

Run 4:

```sh
$ ./runz6 out44m/model44m.bin
achieved tok/s: 16.062241
```

Run 5:

```sh
$ ./runz8 out44m/model44m.bin
achieved tok/s: 45.999730
```

Run 6:

```sh
$ ./runz11 out44m/model44m.bin
 Once upon a time, there was a little girl named Lily. She loved to draw on the blackboard with colorful chalks. One day, Lily couldn't find her favorite blackboard. She looked everywhere for it, but it was nowhere to be found. 
Lily asked her mom and dad if they could buy her a new blackboard, but they couldn't afford it. Lily felt restless and sad. Even though she didn't get to draw on her blackboard, she still had other things to do. 
But then, something unexpected happened. Lily's cat came into her room and started to play with the blackboard. The cat was so busy that it accidentally knocked the blackboard off the shelf and it broke into many pieces. Lily was sad again, but she didn't give up. She decided to draw on a different board instead. And this time, she made sure to put it in a safe place so it wouldn't break again. The end.
<s>
 Once upon a time, there was a little girl named Lily. She loved to wear her red cap everywhere she went. One day, she went to the park to play. She was having so much fun on the swings when she heard a loud noise.
She looked around and saw a little boy crying. She went over to him and asked him what was wrong. He said he lost his red cap and couldn't find it anywhere. Lily told him she would help him look for it.
They searched all over the park, but they couldn't find the cap. Lily started to get worried because it was her favorite cap. They returned an object to the little boy, but it was too late. He had already gone home without his cap.
Lily felt sad and guilty. She realized that it's not very nice to take things that don't belong to her. She learned a valuable lesson that day: it's important to always do the right thing, even if it's hard.
<s>
 Once upon a time, there was a boy named Timmy. Timmy loved to play with his toys and go on adventures. One day, Timmy found a big drawer in his room. He opened it and saw lots of toys inside. Timmy was so eager to play with all his toys, but they were all mixed up. So, Timmy decided to organize them.
He put all the cars in one corner and all the dolls in another corner. He even put all the balls in the last corner. Timmy was so happy to see all his organized toys. He played with them for hours and had so much fun. From that day on, Timmy always remembered to organize his toys and make his room look nice.
<s>
 Once upon a time, there was a little girl named Lily. She loved to play outside in the park. One day, she was playing with her ball when she accidentally dropped it into a puddle. She started to cry because she thought she would never get her ball back.
Suddenly, a police officer walked by and saw Lily crying. He asked her what was wrong and she told him about her ball. The police officer smiled and said, "Don't worry, I'll get your ball for you." He went to his car and drove away.
After a few minutes, the police officer came back with the ball. Lily was so happy and thanked the police officer. The police officer was deaf, but he could still hear her, so Lily waved to him. From that day on, Lily was more careful when she played with her ball, but she also learned that there are kind people who can help when we need it.
<s>
 Once upon a time, in a small town, there was a big festival. All the people were happy. They played games, ate snacks, and danced to music. The town was brilliant.
One day, a little boy named Timmy wanted to stop the festival. He thought if he made the best game, everyone would have more fun. So, he played a game where he hid things. He found a big, shiny ball and hid it in the park.
Soon, people saw Timmy's game and started playing it. But no one could find the ball. They kept looking and looking. Timmy felt sad. He knew that kids should not play tricks. The festival had a big twist. Everyone who forgot to play a game laughed at a silly man.
The moral of the story is: it's better to play and have fun than to play tricks on or make a mess.
<s>
 Once upon a time, in a big tree, there was a nest. In the nest, there were many baby birds. The baby birds
achieved tok/s: 46.642981
```

**Compiler: gcc**

**Model: 15M parameter**

Run 1:

```sh
$ gcc -O3 -o run run.c -lm
$ ./run out/model.bin
 Once upon a time, there was a little girl named Lily. She wanted to bake cookies with her mommy. Her mommy got out a big bowl and filled it with flour, sugar, and eggs. They mixed everything together and put it in the oven. 
While they waited for the cookies to bake, they went to the park. It was a lively park with green trees and flowers. They played on the swings and the slide and had a picnic. After they were done, Lily's mommy took out the whip and showed her how to use it. She explained how to hold it and how to pull the rope. 
Finally, the cookies were done baking in the oven. Lily and her mommy took them out and let them cool down. They were so yummy that Lily asked for toast with butter and jam. They sat down on a lively field and enjoyed their cookies together. Lily was so happy that she got to bake cookies with her mommy.
<s>
 Once upon a time, there was a soldier. He was very organized and liked to keep his toys organized. One day, he went for a walk in the
achieved tok/s: 71.303234
```

Run 2:

```sh
$ gcc -O3 -funsafe-math-optimizations -o run2 run.c -lm
$ ./run2 out/model.bin
 Once upon a time, there was a duck named Quack. Quack loved to swim in the pond all day long. One day, Quack met a careless boy who lived in the same pond. The boy was always throwing things around and not listening to anyone.
Quack decided to teach the boy a lesson about being careful. Quack went to the pond and helped the boy if he fell in. The boy was scared and didn't know what to do. Quack told him to trust him and that everything would be okay.
The boy thought about what Quack said and decided to try it. He became more careful and avoided the danger of being careless. Quack taught the boy how to swim and they became good friends. From that day on, Quack and the boy would always play together and have fun in the pond.
<s>
 Tom and Lily liked to play with their toys. They had many toys, but their favorite was a big red truck. They liked to make the truck go fast or slow, and make it honk. They also liked to serve the truck to their mom and dad, who did not like to play with the truck
achieved tok/s: 165.691289
```

Run 3:

```sh
$ gcc -O3 -Ofast -o run3 run.c -lm
$ ./run3 out/model.bin
 One day, a big cat and a little dog were in the park. They were friends. The cat had dry skin. The dog saw a big stick on the ground. The dog wanted to strike the stick with his paw.
The cat said, "No, don't do that!" The dog thought it was a strange game. He struck the stick very hard. The stick broke into small pieces. The cat and the dog were surprised.
They looked at the broken stick. It was now small and soft. The dog said, "Maybe we can both play with this stick!" They decided to share the stick. They played together in the park, and the cat's dry skin was not so dry anymore.
<s>
 Once upon a time, there was a little girl named Lily. She loved to eat apples, but her mom said she could only have one. Lily was sad because she really wanted to eat an apple. 
One day, Lily's mom made a delicious apple pie. It smelled so good! Lily couldn't wait to eat it. But then she saw a scary spider in the pie. She started to cry, but then she remembered her mom hugging her. 
achieved tok/s: 167.149834

# repeat run 3
$ ./run3 out/model.bin
 One day, a little boy named Tim went to the park with his mom. They took a big, mysterious box with them. Inside the box was a toy car that could move by itself. Tim and his mom played with the car on the grass.
As they played, Tim's mom said, "Tim, you should trust me. There is a secret would happen if you don't tell me." Tim nodded and ran to a hidden spot behind a big tree.
The next day, Tim heard a small sound. He looked and saw a big, talking dog! The dog said, "Hi, Tim! I'm Spin! I can move and play with you!" Tim was so surprised and happy. From that day on, Tim and Spin played with their new friend, the talking dog, all day long.
<s>
 One day, a little girl named Lucy went to the park. She saw a broken toy airplane on the ground. Lucy picked it up and wanted to fix it. She put the broke part of the plane in place. The airplane was fixed!
Lucy took the airplane home to play with. She saw her friend Tim. "Look, Tim!" she said, "This is
achieved tok/s: 161.858438
```

Run 4:

```sh
$ gcc -O3 -Ofast -funsafe-math-optimizations -o run4 run.c -lm
$ ./run4 out/model.bin
 One day, a little girl named Mia found a big hoop in her messy room. She loved to play with the hoop, but she was too small. She wanted to have fun and play with her friends.
Mia went to her friend Tom's house. "Tom, let's play with this hoop!" she said. Tom was not sure. "Put it in the wash." he said. Mia did not listen. She put the hoop in the wash.
But the hoop was not clean. It was very hot. The water made the hoop soft and easy to play with. Mia got sick. Tom was sad. "I told you to put it in, Mia," Tom said. Mia was too sick to play with the hoop, and she stayed sick.
<s>
 One day, a little girl named Sue went to the park. She wanted to play with her friend Max. Max was a small dog with brown fur. They liked to play with a big, red ball. They had lots of fun running and jumping.
"Max, do you want to play fetch?" Sue asked. Max wagged his tail and barked. They played catch with the
achieved tok/s: 171.890412
```

Run 5:

```sh
$ gcc -O3 -march=native -o run5 run.c -lm
$ ./run5 out/model.bin
 Once upon a time, there was a little girl named Lily. She loved to play outside and pick flowers. One day, she saw her friend Sarah who looked sad. Lily asked her what was wrong, and Sarah said she was sad because she did something she wasn't allowed to play outside. 
Lily felt sorry for her friend and decided to play with her. They had so much fun playing together! After they were done playing, Sarah said to Lily, "It's time for you to surrender your love and kindness to someone who needs it." Lily felt happy that she could help her friend feel better. 
From that day on, Lily and Sarah played together every day and Lily knew that she had made a perfect friend. She loved spending time with her and felt happy that she could help someone who needed it.
<s>
 Once upon a time, there was a little girl named Lily. She loved to play in the park with her friends. One day, they saw a big slide. They all took turns sliding down the slide, laughing and having fun. 
But then, Lily's friend got hurt and started to cry. Lily was worried and didn't know what
achieved tok/s: 70.795951
```

Run 6:

```sh
$ gcc -O3 -Ofast -funsafe-math-optimizations -march=native -o run6 run.c -lm
$ ./run6 out/model.bin
 Once upon a time, there was a little boy named Timmy. Timmy was very naughty and always got into fights with his friends. One day, he accidentally tore his favorite toy without his mom lend it to him.
Timmy's mom saw what happened and decided to help him. She searched everywhere in the house, but she couldn't find it either. Finally, she asked Timmy's friend to help her look for it.
They searched high and low, under the bed and in the closet, but the toy was nowhere to be found. Timmy started to feel bad and knew he had hurt his friend's feelings.
The next day, Timmy apologized to his friend and promised to be a good friend from now on. His friend forgave him and they made plans to play together again.
<s>
 Once upon a time, there was a little girl named Lily. She loved to play with her toys, especially her favorite toy, a teddy bear named Brownie. One day, Lily's mom gave her a cane to play with. Lily was so happy to have a new toy to play with.
Brownie, her
achieved tok/s: 153.212953

# repeat run 6
$ ./run6 out/model.bin
 One day, a little green bug went for a walk. The green bug liked to go on walks, but today he wanted to try something new. He saw a big leaf and thought it would be fun to crawl on it.
The green bug tried to crawl on the leaf, but it was difficult. It was hard for his little legs to move. He tried to squeeze off the leaf, but it was too hard. He felt sad and did not know what to do.
Then, a friendly bird came and helped the green bug. The bird pushed the leaf with its beak, and the leaf moved! The green bug was so happy. He tried to crawl on the leaf again, but this time, he was bigger and stronger. The bug and the bird laughed and played together in the park.
<s>
 Once upon a time, there was a young girl named Lily. She loved to play outside in the sunshine. One day, while she was playing in the park, she saw a butterfly. The butterfly was so pretty and colorful.
Lily wanted to catch the butterfly, but it flew away. She felt sad and went home. Her mom asked her why she
achieved tok/s: 149.969830
```

Run 7:

```sh
$ gcc -Ofast -o run7 run.c -lm
$ ./run7 out/model.bin
 Once upon a time, in a small village, there lived a girl named Amy. She had a unique toy, a flute. It was her favorite toy. She loved playing it every day.
One sunny day, Amy took her flute to the park. She played her flute in the calm air. All the birds and bugs came to listen. They clapped their hands and danced. Then, they started to clap along too. They danced and clapped until the sun went down.
The next day, Amy's mom took her to a house. There was a man, a woman, and a little dog. The dog was the owner of the dog. Amy and her mom played with the dog. They all clapped and laughed together. They had a great time at the house.
<s>
 One day, a little girl named Lily went for a walk. It was a noisy day. The birds were singing and the leaves were dancing. Lily loved the noise and wanted to make it quiet.
Lily saw a bird on a tree. The bird was flapping its wings. "Why are you in my tree?" Lily asked the bird. The bird said, "I was playing and
achieved tok/s: 165.108562
```

Run 8:

```sh
$ gcc -O3 -Ofast -o run8 run.c -lm
$ ./run8 out/model.bin 
 One day, a little boy named Tim went to the park with his mom. They went on the swings and the slide. Tim was very happy. He liked to play on the swings.
Tim saw a man who was there. The man had a big hat and a big smile. He said, "Hi, Tim! I have a surprise for you." Tim was alert and surprised. He did not know what the surprise was.
The man opened his big hat and found a funny hat. Tim and his mom laughed. The man said, "I made your hat for my friend Amy. I wanted to make you laugh." Tim smiled and put on the funny hat. He was happy and said, "Thank you, bush!" They played at the park all day, and Tim loved the swing and the funny hat.
<s>
 Once upon a time, there was a kind prince who lived in a big castle. He liked to play with his toys and run around the castle. One day, he went for a walk in the garden and saw a little bird stuck in a tree. The prince knew he had to help the bird, so he climbed the tree and helped the bird fly away. The bird was happy and flew away
achieved tok/s: 150.796423

# repeat run 8
$ ./run8 out/model.bin
 One day, a little girl named Lucy wanted to make a cake. She was very impatient and wanted her cake to be done right. She asked her mom, "Can I mix the cake now?"
Her mom said, "Yes, but be careful not to burn it."
Lucy mixed the cake and put it in the oven. While she was waiting for the cake to be done, something unexpected happened. A small bird flew in through the window and took a tiny bite of her cake.
When the cake was ready, Lucy saw the bird and was surprised. She said, "Oh no! The bird ate my cake!"
Her mom laughed and said, "It's okay, Lucy. The bird just wanted to show you his magic!" They both learned that even small friends can have big jobs and big moments.
<s>
 Once upon a time, there was a boy named Timmy. Timmy loved to fly his kite high up in the sky. The kite was red and shiny. Timmy would run and run, but the kite was too high for him to catch.
One day, Timmy was outside flying his kite when he saw something strange
achieved tok/s: 150.457956
```

**Compiler: gcc**

**Model: 15M parameter**

**Use OpenMP for parallelism**

Run 1:

```sh
$ gcc -O3 -ffast-math -fopenmp -o run1 run.c -lm
$ ./run1 out/model.bin
achieved tok/s: 169.088507
```

Run 2:

```sh
$ gcc -Ofast -fopenmp -o run2 run.c -lm
$ ./run2 out/model.bin 
achieved tok/s: 177.408177
```

Run 3:

```sh
$ gcc -Ofast -ffast-math -fopenmp -o run3 run.c -lm
$ ./run3 out/model.bin
achieved tok/s: 198.295895
```

**Compiler: gcc**

**Model: 44M parameter**

**Use OpenMP for parallelism**

Run 1:

```sh
$ gcc -O3 -ffast-math -fopenmp -o run1 run.c -lm
$ ./run1 out44m/model44m.bin
achieved tok/s: 44.552732
```

Run 2:

```sh
$ gcc -Ofast -fopenmp -o run2 run.c -lm
$ ./run2 out44m/model44m.bin
achieved tok/s: 45.808356
```

Run 3:

```sh
$ gcc -Ofast -ffast-math -fopenmp -o run3 run.c -lm
$ ./run3 out44m/model44m.bin
achieved tok/s: 47.969270
```

**Compiler: gcc**

**Model: 15M parameter**

**Use OpenMP for parallelism**

**Tune specifically for your hardware**

```sh
$ gcc -Ofast -ffast-math -fopenmp -march=native -mtune=native -o run15 run.c -lm
$ ./run15 out/model.bin
achieved tok/s: 200.626959
```

**Model: 44M parameter**

**Use OpenMP for parallelism**

**Tune specifically for your hardware**

```sh
$ gcc -Ofast -ffast-math -fopenmp -march=native -mtune=native -o run15 run.c -lm
$ ./run15 out44m/model44m.bin
achieved tok/s: 47.297921
```

### Cross Compiling

Cross compiling C code with `zig cc`:

- x86_64-linux-musl

```sh
❯ zig cc -O3 -Ofast -ffast-math -o runz11 run.c -lm -target x86_64-linux-musl
❯ file runz11
runz11: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, with debug_info, not stripped

❯ ./runz11 out/model.bin
achieved tok/s: 176.187199
```

- aarch64-linux-gnu

```sh
❯ zig cc -O3 -Ofast -ffast-math -o runz10 run.c -lm -target aarch64-linux-gnu
❯ ./runz10 out/model.bin
exec: Failed to execute process: './runz10' the file could not be run by the operating system.
❯ file runz10
runz10: ELF 64-bit LSB executable, ARM aarch64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, for GNU/Linux 2.0.0, with debug_info, not stripped
```

- aarch64-linux-musl

```sh
❯ zig cc -O3 -Ofast -ffast-math -o runz11 run.c -lm -target aarch64-linux-musl
❯ file runz11
runz11: ELF 64-bit LSB executable, ARM aarch64, version 1 (SYSV), statically linked, with debug_info, not stripped

❯ ./runz11 out/model.bin
exec: Failed to execute process: './runz11' the file could not be run by the operating system.
```

Binary file sizes:

```sh
$ ls -lah runz*
Permissions Size User Date Modified Name
.rwxrwxr-x  108k ggg  24 Jul 23:07  runz2
.rwxrwxr-x  108k ggg  24 Jul 23:08  runz3
.rwxrwxr-x   66k ggg  24 Jul 23:08  runz4
.rwxrwxr-x  106k ggg  24 Jul 23:09  runz5
.rwxrwxr-x  108k ggg  24 Jul 23:10  runz6
.rwxrwxr-x  108k ggg  24 Jul 23:11  runz7
.rwxrwxr-x   66k ggg  24 Jul 23:13  runz8
.rwxrwxr-x   66k ggg  24 Jul 23:15  runz9
.rwxrwxr-x   35k ggg  24 Jul 23:30  runz10
.rwxrwxr-x  104k ggg  24 Jul 23:38  runz11
```

The original README is below.

---

## llama2.c

Have you ever wanted to inference a baby [Llama 2](https://ai.meta.com/llama/) model in pure C? No? Well, now you can!

<img src="assets/llama_cute.jpg" width="300" height="300">

With this code you can train the Llama 2 LLM architecture from scratch in PyTorch, then save the weights to a raw binary file, then load that into one ~simple 500-line C file ([run.c](run.c)) that inferences the model, simply in fp32 for now. On my cloud Linux devbox a dim 288 6-layer 6-head model (~15M params) inferences at ~100 tok/s in fp32, and about the same on my M1 MacBook Air. I was somewhat pleasantly surprised that one can run reasonably sized models (few ten million params) at highly interactive rates with an approach this simple.

Please note that this is just a weekend project: I took nanoGPT, tuned it to implement the Llama-2 architecture instead of GPT-2, and the meat of it was writing the C inference engine in [run.c](run.c). As such, this is not really meant to be a production-grade library right now.

Hat tip to [llama.cpp](https://github.com/ggerganov/llama.cpp) for inspiring this project. I wanted something super minimal so I chose to hard-code the llama-2 architecture, stick to fp32, and just roll one inference file of pure C with no dependencies.

## feel the magic

Let's just run a baby Llama 2 model in C. You need a model checkpoint. Download this 15M parameter model I trained on the [TinyStories](https://huggingface.co/datasets/roneneldan/TinyStories) dataset (~58MB download) and place it into the default checkpoint directory `out`:

```bash
wget https://karpathy.ai/llama2c/model.bin -P out
```

(if that doesn't work try [google drive](https://drive.google.com/file/d/1aTimLdx3JktDXxcHySNrZJOOk8Vb1qBR/view?usp=share_link)). Compile and run the C code:

```bash
gcc -O3 -o run run.c -lm
./run out/model.bin
```

You'll see the text stream a sample. On my M1 MacBook Air this runs at ~100 tokens/s, not bad for super naive fp32 single-threaded C code. See [performance](#performance) for compile flags that can significantly speed this up. Sample output:

*Once upon a time, there was a boy named Timmy. Timmy loved to play sports with his friends. He was very good at throwing and catching balls. One day, Timmy's mom gave him a new shirt to wear to a party. Timmy thought it was impressive and asked his mom to explain what a shirt could be for. "A shirt is like a special suit for a basketball game," his mom said. Timmy was happy to hear that and put on his new shirt. He felt like a soldier going to the army and shouting. From that day on, Timmy wore his new shirt every time he played sports with his friends at the party. Once upon a time, there was a little girl named Lily. She loved to play outside with her friends. One day, Lily and her friend Emma were playing with a ball. Emma threw the ball too hard and it hit Lily's face. Lily felt embarrassed and didn't want to play anymore.
Emma asked Lily what was wrong, and Lily told her about her memory. Emma told Lily that she was embarrassed because she had thrown the ball too hard. Lily felt bad
achieved tok/s: 98.746993347843922*

**Update**: I've now also uploaded a bigger checkpoint. This one is dim 512, 8 layers, 8 heads and context length 1024, a ~44M param Transformer. It trained for 200K iterations batch size 32 on 4XA100 40GB GPUs in ~8 hours. You can use this bigger and more powerful checkpoint like so:

```bash
wget https://karpathy.ai/llama2c/model44m.bin -P out44m
./run out44m/model44m.bin
```

On my MacBook Air compiled with $ gcc -Ofast -o run run.c -lm this ran at ~150 tok/s. Still way too fast! I have to train an even bigger checkpoint... This model samples more coherent and diverse stories:

*Once upon a time, there was a little girl named Lily. She loved playing with her toys on top of her bed. One day, she decided to have a tea party with her stuffed animals. She poured some tea into a tiny teapot and put it on top of the teapot. Suddenly, her little brother Max came into the room and wanted to join the tea party too. Lily didn't want to share her tea and she told Max to go away. Max started to cry and Lily felt bad. She decided to yield her tea party to Max and they both shared the teapot. But then, something unexpected happened. The teapot started to shake and wiggle. Lily and Max were scared and didn't know what to do. Suddenly, the teapot started to fly towards the ceiling and landed on the top of the bed. Lily and Max were amazed and they hugged each other. They realized that sharing was much more fun than being selfish. From that day on, they always shared their tea parties and toys.*

## howto

It should be possible to load the weights released by Meta but I haven't tried because the inference speed, even of the 7B model, would probably be not great with this baby single-threaded C program. So in this repo we focus on more narrow applications, and train the same architecture but from scratch, in this case on the TinyStories dataset for fun.

First let's download and pretokenize some source dataset, e.g. I like [TinyStories](https://huggingface.co/datasets/roneneldan/TinyStories) so this is the only example currently available in this repo. But it should be very easy to add datasets, see the code.

```bash
python tinystories.py download
python tinystories.py pretokenize
```

Then train our model:

```bash
python train.py
```

See the train.py script for more exotic launches and hyperparameter overrides. I didn't tune the hyperparameters, I expect simple hyperparameter exploration should give better models. Totally understand if you want to skip model training, for simple demo just download my pretrained model and save it into the directory `out`:

```bash
wget https://karpathy.ai/llama2c/model.bin -P out
```

Once we have the model.bin file, we can inference in C. Compile the C code first:

```bash
gcc -O3 -o run run.c -lm
```

You can now run it simply as

```bash
./run out/model.bin
```

Watch the tokens stream by, fun! We can also run the PyTorch inference script for comparison (to run, add [model.ckpt](https://drive.google.com/file/d/1SM0rMxzy7babB-v4MfTg1GFqOCgWar5w/view?usp=share_link) to /out if you haven't already):

```bash
python sample.py
```

Which gives the same results. More detailed testing will be done in `test_all.py`, run as:

```bash
$ pytest
```

Currently you will need two files to test or sample: the [model.bin](https://drive.google.com/file/d/1aTimLdx3JktDXxcHySNrZJOOk8Vb1qBR/view?usp=share_link) file and the [model.ckpt](https://drive.google.com/file/d/1SM0rMxzy7babB-v4MfTg1GFqOCgWar5w/view?usp=share_link) file from PyTorch training I ran earlier. I have to think through running the tests without having to download 200MB of data.

## performance

*(NOTE: this guide is not great because I personally spend a lot of my time in Python land and don't have an amazing understanding of a lot of these features and flags. If someone does and is willing to help document and briefly describe some of these and their tradeoffs, I'd welcome a PR)*

There are many ways to potentially speed up this code depending on your system. Here we document a few together with a high-level guide on what they do. Here's again the default way to compile, but using -O3:

```bash
gcc -O3 -o run run.c -lm
```

-O3 includes optimizations that are expensive in terms of compile time and memory usage. Including vectorization, loop unrolling, and predicting branches. Here's a few more to try.

`-Ofast` Run additional optimizations which may break compliance with the C/IEEE specifications, in addition to `-O3`. See [the GCC docs](https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html) for more information.

`-ffast-math` breaks IEEE compliance, e.g. allowing reordering of operations, disables a bunch of checks for e.g. NaNs (assuming they don't happen), enables reciprocal approximations, disables signed zero, etc.

`-funsafe-math-optimizations` a more limited form of -ffast-math, that still breaks IEEE compliance but doesn't have all of the numeric/error handling changes from `-ffasth-math`. See [the GCC docs](https://gcc.gnu.org/wiki/FloatingPointMath) for more information.

`-march=native` Compile the program to use the architecture of the machine you're compiling on rather than a more generic CPU. This may enable additional optimizations and hardware-specific tuning such as improved vector instructions/width.

`-fopenmp` Use OpenMP for parallelism.

`-mtune, -march` Tell the compiler what kind of hardware you have. The flag `-march` tells the compiler about the minimal hardware your code should run on. The other flag `-mtune` is just an optimization hint, you tell the compile to generate code that runs best on your processors. See [Lemire's -mtune, -march in GCC blog post](https://lemire.me/blog/2018/07/25/it-is-more-complicated-than-i-thought-mtune-march-in-gcc/) for more information.

Putting a few of these together, the fastest throughput I saw so far on my MacBook Air (M1) is with:

```bash
gcc -Ofast -o run run.c -lm
```

Also, I saw someone report higher throughput replacing `gcc` with `clang`.

## unsorted todos

- why is there a leading space in C sampling code when we `./run`?
- todo multiquery support? doesn't seem as useful for smaller models that run on CPU (?)
- todo support inferencing beyond max_seq_len steps, have to think through the kv cache
- why is MFU so low (~10%) on my A100 40GB for training?
- weird errors with torch.compile and wandb when using DDP
- make more better tests to decrease yolo

## License
MIT
