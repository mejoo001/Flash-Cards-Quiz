package com.example.flashcards

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import androidx.activity.enableEdgeToEdge
class MainActivity : ComponentActivity() {

    enum class Screen {
        WELCOME, FLASHCARD, SCORE, REVIEW
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()

        setContent {
            var currentScreen by remember { mutableStateOf(Screen.WELCOME) }
            var score by remember { mutableStateOf(0) }
            var questionIndex by remember { mutableStateOf(0) }

            val questions = listOf(
                "The Emulator is a virtual phone running on your computer." to true,
                "Python is a programming language used to develop native android apps" to false,
                "Boolean data types can only hold the values 0 and 1" to false,
                "Android studio has the concept of a project" to true,
                "Kotlin-language has five different data types" to true
            )

            var feedback by remember { mutableStateOf(value ="") }
            var answered by remember { mutableStateOf(false) }

            Column(modifier = Modifier.padding(16.dp)) {

                when (currentScreen) {

                    Screen.WELCOME -> {
                        Text("Welcome to the Flashcard Quiz App!", style = MaterialTheme.typography.headlineSmall)
                        Spacer(modifier = Modifier.height(8.dp))
                        Text("Test your knowledge with true or false questions.")
                        Spacer(modifier = Modifier.height(16.dp))
                        Button(onClick = {
                            score = 0
                            questionIndex = 0
                            currentScreen = Screen.FLASHCARD
                        }) {
                            Text("Start Quiz")
                        }
                    }

                    Screen.FLASHCARD -> {
                        val (questionText, correctAnswer) = questions[questionIndex]
                        Text("Question ${questionIndex + 1} of ${questions.size}", style = MaterialTheme.typography.titleMedium)
                        Spacer(modifier = Modifier.height(8.dp))
                        Text(questionText, style = MaterialTheme.typography.bodyLarge)
                        Spacer(modifier = Modifier.height(16.dp))

                        Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                            Button(onClick = {
                                if (!answered) {
                                    if (correctAnswer) {
                                        score++
                                        feedback = "Correct!"
                                    } else {
                                        feedback = "Wrong!"
                                    }
                                    answered = true
                                }
                            }) {
                                Text("True")
                            }

                            Button(onClick = {
                                if (!answered) {
                                    if (!correctAnswer) {
                                        score++
                                        feedback = "Correct!"
                                    } else {
                                        feedback = "Wrong!"
                                    }
                                    answered = true
                                }
                            }) {
                                Text("False")
                            }
                        }

                        Spacer(modifier = Modifier.height(8.dp))
                        Text(text = feedback)

                        if (answered) {
                            Spacer(modifier = Modifier.height(8.dp))
                            Button(onClick = {
                                answered = false
                                feedback = ""
                                if (questionIndex < questions.lastIndex) {
                                    questionIndex++
                                } else {
                                    currentScreen = Screen.SCORE
                                }
                            }) {
                                Text("Next")
                            }
                        }
                    }

                    Screen.SCORE -> {
                        Text("Quiz Complete!", style = MaterialTheme.typography.headlineSmall)
                        Spacer(modifier = Modifier.height(8.dp))
                        Text("Your Score: $score out of ${questions.size}", style = MaterialTheme.typography.bodyLarge)
                        Spacer(modifier = Modifier.height(8.dp))
                        val finalFeedback = when {
                            score == questions.size -> "Excellent!"
                            score >= questions.size / 2 -> "Good job!"
                            else -> "Keep practising!"
                        }
                        Text(finalFeedback)

                        Spacer(modifier = Modifier.height(16.dp))
                        Button(onClick = {
                            currentScreen = Screen.REVIEW
                        }) {
                            Text("Review Answers")
                        }

                        Spacer(modifier = Modifier.height(8.dp))
                        Button(onClick = {
                            currentScreen = Screen.WELCOME
                        }) {
                            Text("Restart Quiz")
                        }
                    }

                    Screen.REVIEW -> {
                        Text("Review Answers", style = MaterialTheme.typography.titleLarge)
                        Spacer(modifier = Modifier.height(8.dp))
                        questions.forEachIndexed { index, pair ->
                            Text("${index + 1}. ${pair.first} - Answer: ${if (pair.second) "True" else "False"}")
                            Spacer(modifier = Modifier.height(4.dp))
                        }

                        Spacer(modifier = Modifier.height(16.dp))
                        Button(onClick = {
                            currentScreen = Screen.WELCOME
                        }) {
                            Text("Back to Start")
                        }
                    }
                }
            }
        }
    }
}
