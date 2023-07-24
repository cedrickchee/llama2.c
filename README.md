# llama2.c ports

(WIP) Rust, Zig, Go, and wasm port.

## benchmarks

On my Linux laptop (Core i3) this runs at ~150 tokens/s. The fastest throughput I saw so far on Linux laptop is with:

```sh
$ gcc -O3 -Ofast -funsafe-math-optimizations -o run4 run.c -lm
```

See [performance](#performance) for compile flags that can significantly speed this up.

**Runs**

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
