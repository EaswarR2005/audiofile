# Ex.No:3 Develop a simple application to play and control the audio file in android studio.

## NAME : EASWAR R
## REGISTER No : 212223230053


## AIM:

To develop a simple application, to play and control the audio file and to perfrom the start,pause and stop opeartion in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as audiofile and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml and create start,pause and stop button.

Step 6: Display message give in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to play and control the audio file”.
Developed by: EASWAR R
Registeration Number : 212223230053
*/
```

## MAIN_ACTIVITY.JAVA :

```
package com.example.audiofile;

import android.media.AudioAttributes;
import android.media.MediaPlayer;
import android.media.audiofx.LoudnessEnhancer;
import android.os.Bundle;
import android.os.Handler;
import android.widget.Button;
import android.widget.SeekBar;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    private MediaPlayer mediaPlayer;
    private LoudnessEnhancer loudnessEnhancer;
    private SeekBar seekBar;
    private final Handler handler = new Handler();
    private Runnable runnable;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button btnPlay = findViewById(R.id.btnPlay);
        Button btnPause = findViewById(R.id.btnPause);
        Button btnStop = findViewById(R.id.btnStop);
        seekBar = findViewById(R.id.seekBar);

        btnPlay.setOnClickListener(v -> {
            if (mediaPlayer == null) {
                try {
                    // Create MediaPlayer
                    mediaPlayer = MediaPlayer.create(this, R.raw.audiofile);
                    
                    if (mediaPlayer != null) {
                        // Ensure it uses the media/music stream
                        mediaPlayer.setAudioAttributes(new AudioAttributes.Builder()
                                .setUsage(AudioAttributes.USAGE_MEDIA)
                                .setContentType(AudioAttributes.CONTENT_TYPE_MUSIC)
                                .build());

                        // Set internal volume to maximum
                        mediaPlayer.setVolume(1.0f, 1.0f);

                        // Use LoudnessEnhancer to boost the volume further
                        // 2000mB (millibels) is a 20dB boost.
                        loudnessEnhancer = new LoudnessEnhancer(mediaPlayer.getAudioSessionId());
                        loudnessEnhancer.setTargetGain(2000); 
                        loudnessEnhancer.setEnabled(true);
                        
                        setupSeekBar();
                        mediaPlayer.start();
                        updateSeekBar();
                        Toast.makeText(this, "Playing with Boost", Toast.LENGTH_SHORT).show();
                        mediaPlayer.setOnCompletionListener(mp -> stopPlayer());
                    } else {
                        Toast.makeText(this, "Error: Could not load audio file", Toast.LENGTH_LONG).show();
                    }
                } catch (Exception e) {
                    Toast.makeText(this, "Playback failed", Toast.LENGTH_SHORT).show();
                }
            } else {
                mediaPlayer.start();
                if (loudnessEnhancer != null) {
                    loudnessEnhancer.setEnabled(true);
                }
                updateSeekBar();
                Toast.makeText(this, "Playing", Toast.LENGTH_SHORT).show();
            }
        });

        btnPause.setOnClickListener(v -> {
            if (mediaPlayer != null && mediaPlayer.isPlaying()) {
                mediaPlayer.pause();
                handler.removeCallbacks(runnable);
                Toast.makeText(this, "Paused", Toast.LENGTH_SHORT).show();
            }
        });

        btnStop.setOnClickListener(v -> stopPlayer());

        seekBar.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                if (fromUser && mediaPlayer != null) {
                    mediaPlayer.seekTo(progress);
                }
            }

            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {}

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {}
        });
    }

    private void setupSeekBar() {
        if (mediaPlayer != null) {
            seekBar.setMax(mediaPlayer.getDuration());
        }
    }

    private void updateSeekBar() {
        if (mediaPlayer != null && mediaPlayer.isPlaying()) {
            seekBar.setProgress(mediaPlayer.getCurrentPosition());
            runnable = this::updateSeekBar;
            handler.postDelayed(runnable, 1000);
        }
    }

    private void stopPlayer() {
        if (loudnessEnhancer != null) {
            loudnessEnhancer.release();
            loudnessEnhancer = null;
        }
        if (mediaPlayer != null) {
            handler.removeCallbacks(runnable);
            mediaPlayer.release();
            mediaPlayer = null;
            seekBar.setProgress(0);
            Toast.makeText(this, "Stopped", Toast.LENGTH_SHORT).show();
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        stopPlayer();
    }
}


```

## ACTIVITY_MAIN.XML :

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="24dp"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/tvTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Audio Player"
        android:textSize="24sp"
        android:textStyle="bold"
        app:layout_constraintBottom_toTopOf="@+id/seekBar"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_chainStyle="packed" />

    <SeekBar
        android:id="@+id/seekBar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="32dp"
        android:layout_marginBottom="32dp"
        app:layout_constraintBottom_toTopOf="@+id/controls"
        app:layout_constraintTop_toBottomOf="@+id/tvTitle" />

    <LinearLayout
        android:id="@+id/controls"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/seekBar">

        <Button
            android:id="@+id/btnPlay"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginEnd="8dp"
            android:text="Play" />

        <Button
            android:id="@+id/btnPause"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginEnd="8dp"
            android:text="Pause" />

        <Button
            android:id="@+id/btnStop"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Stop" />

    </LinearLayout>

</androidx.constraintlayout.widget.ConstraintLayout>

```


## OUTPUT

<img width="373" height="839" alt="image" src="https://github.com/user-attachments/assets/24ee1192-dc05-4b44-b6a8-3adfd650bf93" />

<img width="373" height="837" alt="image" src="https://github.com/user-attachments/assets/eea279ec-e570-4d12-9105-4a535024eebb" />

<img width="375" height="841" alt="image" src="https://github.com/user-attachments/assets/5aab1045-cece-4a49-a176-2f82665c3d43" />

## RESULT
   Thus a simple application, to play and control the audio file and to perfrom the start,pause and stop opeartion in Android Studio is developed and executed successfully.
