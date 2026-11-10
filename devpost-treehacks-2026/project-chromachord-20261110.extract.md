## Inspiration

The inspiration behind this idea is to have the most extensive overview of music theory, especially in Jazz. Using a physics based approach, we could correctly identify all ~4000 chords in an octave and rank them by dissonance. With the power of LLMs in today's world, it is easier than ever to offload the burden of improvisation to LLMs. However, it was important to us in the beginning of this process to stray away from the LLM models that have little interpretability. This way of constructing the chords and ranking their dissonance from the ground up builds concrete understanding of which chord works well with what.

Now, introducing ChromaChord, a solution at the intersection of acoustics, graph theory, and a strong desire to have better tools for learning how to play jazz!

## What it does

ChromaChord is a real-time AI jazz assistant that listens to your improvisation and helps you navigate harmony.

As you play, it analyzes incoming notes using chroma vector representations, maps harmonic relationships onto a graph of possible chord transitions, and suggests musically coherent next chords.

Of course, humans have limitations, and there are only so many paths we can physically traverse. So after the extensive sifting with the chroma vectors, we can use AI to generate harmony, melodies, matching the progression that was played.

## How we built it

Part 1: Chroma Vectors

- MIDI input is captured live from guitar/keyboard.
- Notes are converted into chroma vectors to represent pitch class content.
- A graph-based harmonic model encodes possible chord transitions and overtone relationships.
- Deterministic tools handle chord detection, key inference, and progression suggestions.

We did our literature review for this project to convert the theory to code. Here are some papers we read:

Part 2: AI Features We wanted to think creatively about how to incorporate the best AI tools to enhance our product.

- The Perplexity Sonar API is queried to retrieve popular songs that also follow a similar chord progression. This helps with inspiration and enhancing how you think about future directions.

We separated fast local harmonic analysis from slower AI reasoning to maintain responsiveness during live play.

## Challenges we ran into

Real-time Translation

- Problem: Signal processing for instruments is noisy and still an open problem.
- Solution: Using direct MIDI input from our keyboard
- Chord ambiguity
- Problem: Translating chroma vectors into user input. Multiple harmonic interpretations can exist for the same pitch set.
- Problem: Avoiding random suggestions and diluting our product.
- Solution: Incorporating the Perplexity Sonar API and Claude Agent SDK in an intentional way. Synchronization

## Accomplishments that we're proud of

Real-time harmonic graph visualization driven by live chroma data. Generating backing tracks dynamically from user-generated chord progressions. Integrating signal processing and graph theory into one coherent system. Building a system that feels like collaboration, not automation.

- All of the musical friends we made along the way!

## What we learned

Creative AI tools are most compelling when they enhance, not replace, human expression. Harmony is surprisingly graph-like — functional relationships translate naturally into network structures. AI works best as a decision layer on top of deterministic musical analysis. Real-time systems require architectural separation between fast analysis and slower reasoning.

## What's next for ChromaChord

- Personalized skill modeling and long-term learning for music.
- Support for even more musical instruments, like guitars.
- Expanded harmonic graph modeling using learned embeddings.
- Voice-based interaction (“make it darker,” “more tension”)

## Built With

- claude
- nextjs
- perplexity
- python
- vercel
