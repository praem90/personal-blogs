---
title: "Journey from Python Script to Native Desktop AI App"
sub_title: Semantic Video Search for Video Editors
author: Mohan Raj M (@praem90)
theme:
  name: terminal-dark
---

The Motivation
===
 * Build a new micro sass product
 * Leverage the prior experience with Live Streaming and Curiosity to learn and apply AI in a practical way
 * Chose Media as Domain and AI as tool and started identifying the problem statement to solve

 <!-- end_slide -->

The Problem: The "Flashback" 
===
 * Spoke directly with a TV Serial Content Editor 
 * Finding an old scene from a TV serial is a tedious and time-consuming task from a dozens of unindexed or poorly indexed hard disks
 * The editor has to plug multiple hard disks, open each video file, and manually search for the required scene

 <!-- speaker_note: Insert the billion doller idea meme -->
 <!-- end_slide -->

The Solution: Semantic Video Search
===
 * Build a semantic video search engine using RAG
 * Should be able to handle large video files and provide fast search results
 * Since the video files are large and proprietary, it should be a local solution
 * Should create thumbnails for the search results to quickly identify the scene
 * Must be tagged with the hard disk name and the file path 
 * Should be able to drag and drop to their video editing software for further processing

<!-- speaker_note: Insert the rukko jaro meme -->
<!-- end_slide -->

The MVP: Python Script
===
 * **MVP Tech Stack:** OpenCV, Sentence Transformers, Vector Database and FastAPI
 * The sentence-transformers provides a simple way to get the embeddings for the video frames and the search queries
 * The vector database provides a simple way to store and query the embeddings
 * There comes the LaunceDB a vector database similar to Sqlite. Plug and Play but powerful for use case 
 * Its embeded into the app rather than spinning a seperated database server.
 * OpenCV reads the video and extract a frame for every second and get the embeddings for the frame using the sentence-transformers
 * Store the filepath, timestamp and the embeddings in the LaunceDB
 * When user queries, get the embeddings for the query using the sentence-transformers and query the LaunceDB for the most similar embeddings and return the file path and timestamp for the results
 * The confidence was very poor.
 * CLIP model description might reduces the confidence.
 * Then the embedding cosine similarity which reduces the confidence even more.
 * In our case 25% of confidence is the best match.
 * The confidence was mapped 0-25% to 0-100% for the user to understand the confidence of the result.

<!-- end_slide -->

The Pivot: From Python Script to Native Desktop App
===

 * The script was not user-friendly and required technical knowledge to run
 * To make the solution accessible to non-technical users, we decided to build a native desktop application with a graphical user interface (GUI)
 * The app would allow users to drag and drop video files, enter search queries, and view search results with thumbnails and metadata
 * Considere Electron and Tauri for building the desktop app, but chose Tauri for its smaller bundle size and better performance
 * Impresse by Tauri's developer experience
 * Rust as the backend language for Tauri, which I knew some basics of, but needed not to learn more to build the app
 * The frontend would be built using React and tailwindcss, which I was already familiar with
 * But the AI engine was served by a python fastapi server, which was a challenge to integrate with Tauri
 * The solution was to run the FastAPI server as a separate process and communicate with it via HTTP requests from the Tauri app

 <!-- speaker_note: React+Tailwindcss -> Tauri -> Engine -->

<!-- end_slide -->

Packaging
===
 * The app runs smoothly but when it comes to packing the app for distribution, the FastAPI server was not included in the Tauri bundle
 * We cannot expect the users to install Python and the required dependencies to run the FastAPI server
 * The solution was to package the FastAPI server as a standalone executable using PyInstaller and include it in the Tauri bundle
 * Using the sidecar pattern, tauri can run the FastAPI server as a separate process and communicate with it via HTTP requests
 * The Problems
   * The fast api needs to be listened on a port, but the port may be in use by another process
   * Slower startup time for the FastAPI server, which may cause the Tauri app to fail to connect to it
   * The bundle size of the app increases due to the inclusion of the python, FastAPI server and its dependencies

<!-- end_slide -->

The End Game
===
 * Decided to use a different approach to avoid the issues with the FastAPI server
   * How to run the AI inference in Rust
   * What about the database?
   * Limited Rust knowledge
 * But its the time to learn Rust and build the AI engine in purely Rust
 * Then got to know that the launceDB was written in Rust and the python SDK was just a wrapper around the Rust code
 * Got to know that the sentence-transformers are nothing but a wrapper around the HuggingFace CLIP model and uses Pytorch for inference
 * HuggingFace has it own Rust SDK for inference, Candle
 * There is no alternative to OpenCV in Rust, but FFMpeg helped
 * But the docs are very limited, not all the models are supported for Rust inference

<!-- end_slide -->

Putting It All Together
===
 * Its the most tricky part of the journey, to put it all together and make it work in Rust, but its a great learning experience
 * The LaunceDB and its Schema
 * Figuring out the apis to get the embeddings from Candle and the CLIP model
 * Deling with FFMpeg to meet the OpenCV performance
 * The Tauri app state and async
 * The final architecture of the app is as follows:


<!-- end_slide -->

The Roadmap
===
 * Might need to add the ability to index the audio and subtitle files in the future
   * To index the audio, we can use Whisper to transcribe the audio
   * Each dialog will comes with the timestamp range
   * While quering, we should make two database calls, one for the video and one for the audio, and merge the results based on the timestamp range
 * Use local LLMs as an Agent to help the user with the search queries and provide more relevant results
 * Add more MCP tools to do more with the search results, like trimming, merging, adding effects, etc.




