# RBC Borealis CTE Detection

Detecting head impacts in game footage and building an impact exposure profile for each athlete.

## The problem

Repeated head impacts in contact sports like football, hockey, and rugby are linked to long-term neurological damage such as CTE. Tracking every collision a player takes is hard. Staff can't watch every player on every play, and helmet sensors are expensive, so most university, amateur, and youth teams go without.

Most teams already record their games, though. If we can pull head-impact information straight out of that footage, this kind of monitoring gets a lot more accessible.

## What we're building

Given game video and player tracking data, the system should figure out:

- when a head impact happens
- which athlete took it
- what kind of collision it was (helmet-to-helmet, helmet-to-body, helmet-to-shoulder, helmet-to-ground)
- how the players were moving going into it
- how often impacts are happening

Those events add up over time into a profile per athlete, so a trainer could see something like:

```
Player 24
  12 head impacts this game
  3 high-speed collisions
  2 helmet-to-helmet impacts
  3 impacts within a short window
  Elevated exposure, review recommended
```

This is not a diagnostic tool. It doesn't detect concussions or CTE. It just gives coaches and medical staff a quick way to find and review the collisions worth looking at.

## Pipeline

```
Game video + player tracking
  -> helmet and player tracking
  -> head impact detection
  -> collision characteristics
  -> athlete exposure profile
  -> elevated risk flag
```

A single frame usually isn't enough to tell whether an impact happened, so the model needs to look at how players move before, during, and after contact. Vision handles finding and tracking helmets. Tracking data adds position, speed, acceleration, and direction for each player.

## Data

We're using the [NFL 1st and Future Impact Detection](https://www.kaggle.com/competitions/nfl-impact-detection) dataset from Kaggle. It has synchronized play videos, labelled helmet boxes, player identities, labelled impacts with impact types, and player tracking data.

To download it (needs the Kaggle CLI and an API token, and you have to accept the competition rules first):

```bash
kaggle competitions download -c nfl-impact-detection -p data/
unzip data/nfl-impact-detection.zip -d data/
```

The `data/` folder is gitignored.

## Setup

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Repo layout

```
data/        dataset goes here (not committed)
notebooks/   exploration and experiments
src/         pipeline code
```

## Future work

Pairing these exposure profiles with actual medical injury data could let us train models that estimate concussion or long-term injury risk directly.
