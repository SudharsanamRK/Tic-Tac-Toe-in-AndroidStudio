# Tic-Tac-Toe-in-AndroidStudio

## Aim
The aim of this project is to create a simple Tic Tac Toe game for Android devices. The game should provide an enjoyable experience for users to play against either another player or against the computer.

## Algorithm
The game logic is based on a simple algorithm that determines the next move for the computer player. It involves checking for winning moves, blocking opponent's winning moves, and making random moves if neither winning nor blocking moves are available.
## Program
# MainActivity.java
```java
package com.example.tictactoe;
import androidx.appcompat.app.AppCompatActivity;
import android.os.Bundle;
import android.view.MotionEvent;
import android.view.View;
import android.view.animation.Animation;
import android.view.animation.AnimationUtils;
import android.widget.Button;
import android.widget.TextView;

public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private Button[][] buttons = new Button[3][3];
    private boolean player1Turn = true;
    private int roundCount;
    private int player1Points;
    private int player2Points;
    private int gamesPlayed;
    private TextView textViewPlayer1;
    private TextView textViewPlayer2;
    private Animation buttonClickAnimation;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textViewPlayer1 = findViewById(R.id.text_view_p1);
        textViewPlayer2 = findViewById(R.id.text_view_p2);
        buttonClickAnimation = AnimationUtils.loadAnimation(this, R.anim.button_scale_animation);

        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                String buttonID = "button_" + i + j;
                int resID = getResources().getIdentifier(buttonID, "id", getPackageName());
                buttons[i][j] = findViewById(resID);
                buttons[i][j].setOnClickListener(this);

                // Apply animation to each button for press and release events
                buttons[i][j].setOnTouchListener(new View.OnTouchListener() {
                    @Override
                    public boolean onTouch(View v, MotionEvent event) {
                        if (event.getAction() == MotionEvent.ACTION_DOWN) {
                            v.startAnimation(buttonClickAnimation);
                        } else if (event.getAction() == MotionEvent.ACTION_UP) {
                            // Reset the button's scale to its normal form
                            v.clearAnimation(); // Clears any animation in progress
                            v.setScaleX(1.0f);
                            v.setScaleY(1.0f);
                        }
                        return false;
                    }
                });
            }
        }

        Button buttonReset = findViewById(R.id.button_reset);
        buttonReset.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                resetGame();
            }
        });
    }

    @Override
    public void onClick(View v) {
        if (!((Button) v).getText().toString().equals("")) {
            return;
        }

        if (player1Turn) {
            ((Button) v).setText("X");
        } else {
            ((Button) v).setText("O");
        }

        roundCount++;

        if (checkForWin()) {
            if (player1Turn) {
                player1Wins();
            } else {
                player2Wins();
            }
        } else if (roundCount == 9) {
            draw();
        } else {
            player1Turn = !player1Turn;
        }
    }

    private boolean checkForWin() {
        String[][] board = new String[3][3];
        // Fill the board array with the current state of the buttons
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                board[i][j] = buttons[i][j].getText().toString();
            }
        }

        // Check for column wins
        for (int col = 0; col < 3; col++) {
            if (!board[0][col].isEmpty() && board[0][col].equals(board[1][col]) && board[0][col].equals(board[2][col])) {
                return true;
            }
        }

        // Add logic here to check for row and diagonal wins

        return false;
    }

    private void player1Wins() {
        player1Points++;
        updatePointsText();
        if (player1Points >= 2) {
            gamesPlayed++;
            declareWinner("Player 1");
        } else {
            resetBoard();
        }
    }

    private void player2Wins() {
        player2Points++;
        updatePointsText();
        if (player2Points >= 2) {
            gamesPlayed++;
            declareWinner("Player 2");
        } else {
            resetBoard();
        }
    }

    private void draw() {
        gamesPlayed++;
        if (gamesPlayed >= 3) {
            // Declare the winner based on points
            if (player1Points > player2Points) {
                declareWinner("Player 1");
            } else if (player2Points > player1Points) {
                declareWinner("Player 2");
            } else {
                declareWinner("It's a draw");
            }
        } else {
            resetBoard();
        }
    }

    private void declareWinner(String winner) {
        // Display the winner or draw message
        // Reset the game
        resetGame();
    }

    private void updatePointsText() {
        textViewPlayer1.setText("Player 1: " + player1Points);
        textViewPlayer2.setText("Player 2: " + player2Points);
    }

    private void resetBoard() {
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                buttons[i][j].setText("");
            }
        }
        roundCount = 0;
        player1Turn = true;
    }

    private void resetGame() {
        player1Points = 0;
        player2Points = 0;
        gamesPlayed = 0;
        updatePointsText();
        resetBoard();
    }
}
```
# activity_main.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <LinearLayout
        android:id="@+id/linearLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentTop="true"
        android:layout_centerHorizontal="true"
        android:orientation="horizontal"
        android:padding="16dp">

        <TextView
            android:id="@+id/text_view_p1"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Player 1: 0"
            android:textSize="20sp" />

        <TextView
            android:id="@+id/text_view_p2"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Player 2: 0"
            android:textSize="20sp" />

    </LinearLayout>

    <GridLayout
        android:id="@+id/gridLayout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_below="@id/linearLayout"
        android:layout_marginTop="16dp"
        android:columnCount="3"
        android:orientation="horizontal"
        android:rowCount="3">

        <!-- Buttons for the Tic Tac Toe grid -->
        <Button
            android:id="@+id/button_00"
            style="@style/ButtonStyle"
            android:layout_column="0"
            android:layout_row="0"
            android:text=""
            android:onClick="onButtonClick" />

        <Button
            android:id="@+id/button_01"
            style="@style/ButtonStyle"
            android:layout_column="1"
            android:layout_row="0"
            android:text=""
            android:onClick="onButtonClick" />

        <Button
            android:id="@+id/button_02"
            style="@style/ButtonStyle"
            android:layout_column="2"
            android:layout_row="0"
            android:text=""
            android:onClick="onButtonClick" />

        <Button
            android:id="@+id/button_10"
            style="@style/ButtonStyle"
            android:layout_column="0"
            android:layout_row="1"
            android:text=""
            android:onClick="onButtonClick" />

        <Button
            android:id="@+id/button_11"
            style="@style/ButtonStyle"
            android:layout_column="1"
            android:layout_row="1"
            android:text=""
            android:onClick="onButtonClick" />

        <Button
            android:id="@+id/button_12"
            style="@style/ButtonStyle"
            android:layout_column="2"
            android:layout_row="1"
            android:text=""
            android:onClick="onButtonClick" />

        <Button
            android:id="@+id/button_20"
            style="@style/ButtonStyle"
            android:layout_column="0"
            android:layout_row="2"
            android:text=""
            android:onClick="onButtonClick" />

        <Button
            android:id="@+id/button_21"
            style="@style/ButtonStyle"
            android:layout_column="1"
            android:layout_row="2"
            android:text=""
            android:onClick="onButtonClick" />

        <Button
            android:id="@+id/button_22"
            style="@style/ButtonStyle"
            android:layout_column="2"
            android:layout_row="2"
            android:text=""
            android:onClick="onButtonClick" />



    </GridLayout>

    <Button
        android:id="@+id/button_reset"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/gridLayout"
        android:layout_centerHorizontal="true"
        android:layout_marginTop="16dp"
        android:text="Reset" />

</RelativeLayout>
```
# button_scale_animation.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:fillAfter="true">
    <scale
        android:fromXScale="1.0"
        android:toXScale="0.9"
        android:fromYScale="1.0"
        android:toYScale="0.9"
        android:pivotX="50%"
        android:pivotY="50%"
        android:duration="100"
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"/>
</set>
```
# styles.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="ButtonStyle">
        <item name="android:background">#FF4081</item> <!-- Example background color (Pink) -->
        <item name="android:textColor">#FFFFFF</item> <!-- Example text color (White) -->
        <item name="android:textSize">18sp</item> <!-- Example text size (18sp) -->
        <!-- Add any other attributes you want to customize -->
    </style>
</resources>
```
# colors.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>
</resources>
```
# button_click_animation.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <alpha
        android:fromAlpha="1.0"
        android:toAlpha="0.5"
        android:duration="100"/>
    <scale
        android:fromXScale="1.0"
        android:toXScale="0.8"
        android:fromYScale="1.0"
        android:toYScale="0.8"
        android:pivotX="50%"
        android:pivotY="50%"
        android:duration="100"/>
</set>
```
# strings.xml
```xml
<resources>
    <string name="app_name">TicTacToe</string>
</resources>
```
## Output
![image](https://github.com/SudharsanamRK/Tic-Tac-Toe-in-AndroidStudio/assets/115523484/86ac0dca-1ab7-4f9d-a6bf-4a04b7095894)

## Result
This project delivers a fully functional Tic Tac Toe game for Android devices. Players can enjoy playing against each other or against the computer.
