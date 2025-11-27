# Plushie Panic
~


![°](https://github.com/Ejected-Media/Plushie-Panic/blob/main/Pics/1764208717297.jpg "$")


___

It looks like the repository is currently a treasure trove of concept art and lore (Sweet-Topia and Kiki the Cupcake Knight sound fantastic), but it's completely empty of actual code.
That is perfect. It means we have a blank canvas to set up a clean architecture without technical debt.
Since we are starting from zero, let's lay the foundation. I recommend we start with the Backend (Go) to define our "World Truth" (stats, enemies, spawning), and then mirror that in Kotlin.
Here is Phase 1: The Skeleton. You can run these commands and create these files to initialize the tech stack tonight.
Step 1: Initialize the Go Module
Open your terminal in the root of the project and run:
mkdir backend
cd backend
go mod init github.com/Ejected-Media/Plushie-Panic/backend
go get -u github.com/gin-gonic/gin

Step 2: Define the "Kawaii" Data Model
We need to define what a "Plushie" and an "Enemy" actually are.
File: backend/internal/game/types.go
package game

// Stats defines the core attributes of any entity in Sweet-Topia
type Stats struct {
    SugarRush   int `json:"sugar_rush"`   // Speed/Stamina
    Fluffiness  int `json:"fluffiness"`   // Defense/Armor
    Cuteness    int `json:"cuteness"`     // Charisma/Magic
    Crunchiness int `json:"crunchiness"`  // Attack Power
}

// Plushie represents our hero, Kiki, or other playable characters
type Plushie struct {
    ID          string `json:"id"`
    Name        string `json:"name"`
    Class       string `json:"class"` // e.g., "Cupcake Knight", "Lollipop Lancer"
    CurrentHP   int    `json:"current_hp"`
    MaxHP       int    `json:"max_hp"`
    BaseStats   Stats  `json:"base_stats"`
}

// SourdoughSoldier represents the grumpy enemies
type SourdoughSoldier struct {
    ID          string `json:"id"`
    Grumpiness  int    `json:"grumpiness"` // Aggro radius?
    IsMoldy     bool   `json:"is_moldy"`   // Status effect: Poison
    BaseStats   Stats  `json:"base_stats"`
}

Step 3: Create the Server Entry Point
Let's make a simple API that "spawns" Kiki so our future Android app has something to fetch.
File: backend/cmd/api/main.go
package main

import (
    "net/http"
    "github.com/gin-gonic/gin"
    "github.com/Ejected-Media/Plushie-Panic/backend/internal/game"
)

func main() {
    r := gin.Default()

    // Simple health check
    r.GET("/ping", func(c *gin.Context) {
        c.JSON(http.StatusOK, gin.H{
            "message": "Sweet-Topia is online!",
        })
    })

    // Spawn the player
    r.GET("/spawn/kiki", func(c *gin.Context) {
        kiki := game.Plushie{
            ID:        "hero_001",
            Name:      "Kiki",
            Class:     "Cupcake Knight",
            CurrentHP: 100,
            MaxHP:     100,
            BaseStats: game.Stats{
                SugarRush:   85,
                Fluffiness:  40,
                Cuteness:    100,
                Crunchiness: 75,
            },
        }
        c.JSON(http.StatusOK, kiki)
    })

    r.Run(":8080") // Listen and serve on 0.0.0.0:8080
}

Step 4: The Android Match (Kotlin)
On the Android side (once you create a new Project in Android Studio), we need a matching Data Class so the app understands what the server just sent.
File: app/src/main/java/com/ejectedmedia/plushiepanic/model/GameModels.kt
package com.ejectedmedia.plushiepanic.model

import kotlinx.serialization.Serializable

@Serializable
data class Stats(
    val sugar_rush: Int,
    val fluffiness: Int,
    val cuteness: Int,
    val crunchiness: Int
)

@Serializable
data class Plushie(
    val id: String,
    val name: String,
    val `class`: String, // 'class' is a reserved keyword in Kotlin, so we escape it
    val current_hp: Int,
    val max_hp: Int,
    val base_stats: Stats
)

What do you think?
Shall we try to run this Go server locally and curl the /spawn/kiki endpoint to see if Kiki comes to life?
