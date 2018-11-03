---
title: what does exactly once really mean in stream processing
tags: stream processing
---

On using Apache Storm, a powerful stream processing engine, I felt quite confused about the exactly once semantic. After searching and reading several papers, I got a little clear of it.

Actually, Storm supplies other options beyond exactly once, namely the semantic of at least once or at most once which are quite easy to understand. At most once means that Storm only emits the message once without any retry even failure or error arise. This is a best effort service without any garantee of success.

While in the other side, at-least-once urgies operators of Storm to retry encountering failure or error until an acknowledge of success arrives.

# exactly-once

Literally, exacly-once means the message is processed only once. How will the operators behaviour facing failure or error, repeat it or not?
