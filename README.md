# Mate, Where's My Car?

Public project overview for an interactive machine-learning web app that helps
estimate where a parked car most likely ended up from a small trail of recent
trips.

The app asks a practical modelling question:

> If someone can remember roughly when they travelled, how far they went, and
> whether the trip started from home or somewhere else, can a model reconstruct
> the most likely parking trail?

This repository is a documentation and project-overview companion for the
deployed app. It intentionally does not include the full implementation at this
stage.

## Live app

Live app: [wheresmycar.daguilardo.com](https://wheresmycar.daguilardo.com)

## At a Glance

- **Project type:** public machine-learning demo and interactive web app.
- **Core idea:** infer a simple parking trail from user-entered trip times,
  distances, and Home vs Other location states.
- **User input:** a few recent trips, including date, start time, end time,
  approximate distance, and initial start-location choice.
- **Model output:** a best-guess final location: **Home** or **Other**.
- **Serving model:** the frontend sends trip details to a backend API, which
  returns model-based start and end predictions with confidence values.
- **Important caveat:** this is a playful modelling demo, not a GPS tracker,
  parking record, navigation tool, or guarantee of where a vehicle is.

## What Home and Other Mean

The app keeps the location labels deliberately simple:

- **Home** means the user's house.
- **Other** means anywhere else: work, the shops, a mate's place, or anywhere
  that is not home.

The app does not try to identify the exact street, building, car park, or
postcode. It only estimates whether the car is most likely at home or not at
home.

## What the App Does

The app lets a user build a short trip trail by entering recent journeys. For
each trip, it records:

- The date.
- The start and end time.
- The rough distance in miles.
- The starting location for the first trip: Home, Other, or Not sure.

After the first trip, the app carries the last predicted location forward as the
start of the next trip. This keeps the trail connected instead of treating every
journey as unrelated.

When the user follows the trail, the app returns:

- The most likely final location.
- A visual timeline of the trip sequence.
- Per-trip prediction cards.
- Start-location checks and end-location confidence scores.
- Technical details for users who want to inspect what happened.
- A CSV download of the trip and prediction data.

## How to Read It

The app combines simple user-entered trip details with machine-learning
classification. Read the result as a best guess based on travel patterns, not as
a measured location.

Two mechanics matter most:

- **Independent start and end checks:** one model checks where a trip probably
  started, while another predicts where it probably ended.
- **Trail continuity:** once a trip ends, that location is carried forward as
  the start of the next trip, so the sequence behaves like one connected trail.

If the carried-forward start location disagrees with the model's own start
check, the app shows that too. This helps users spot trips that may deserve a
second look.

## Methodology

### Pipeline

```text
user-entered trip details
  -> validation and plausibility checks
  -> start-location classification
  -> end-location classification
  -> carry-forward trail logic
  -> confidence labelling
  -> final Home vs Other estimate
  -> visual timeline and CSV export
```

The model uses travel-pattern features such as time of day, trip duration,
distance, date context, and trip sequence. It does not use GPS coordinates,
accounts, maps, live vehicle data, or personal tracking.

### Machine-Learning Model

The app uses a machine-learning model trained on real-world travel behaviour
data. The aim is not to know where a specific car is with certainty, but to learn
patterns that make one outcome more likely than another.

In simple terms, the model asks:

- Does this journey look more like it started from home or somewhere else?
- Does this journey look more like it ended at home or somewhere else?
- Given the previous trip, what is the most consistent connected trail?

The visible confidence scores indicate how sure the model is about each
prediction. They are useful context, but they should not be treated as proof.

### Plausibility Checks

The app includes basic validation and warning checks so obviously unrealistic
trips do not pass unnoticed.

For example:

- Very short trips must still have a minimum duration.
- Very small distances are rejected.
- Trips with unrealistic implied speeds can trigger a warning before prediction.

The user can still continue after a warning, but the app makes clear that the
model works best with roughly believable trips.

## Data Source

The project is based on travel-behaviour modelling using real-world trip data,
including the UK's National Travel Survey.

| Source | Role |
| --- | --- |
| [National Travel Survey](https://www.gov.uk/government/collections/national-travel-survey-statistics) | Travel behaviour, journey timing, trip-distance context, and Home vs Other-style pattern learning |

The source data is used for modelling travel behaviour. The public app does not
expose raw survey records.

## Privacy and Safety

The app is designed as a no-GPS, no-account demo.

It does not require:

- GPS access.
- A user account.
- A vehicle connection.
- A map location.
- A postcode.
- A number plate.
- Live tracking data.

The user manually enters approximate trip details. The app only returns a broad
Home vs Other estimate.

## Assumptions and Limits

The app is intentionally simple and assumption-driven. Key interpretation points:

- **Home** and **Other** are broad labels, not precise places.
- The app does not know the exact location of a car.
- The model can be wrong, especially if the trip details are incomplete,
  unusual, or unrealistic.
- Confidence scores reflect model certainty, not real-world proof.
- The app works best with a short, plausible sequence of recent trips.
- It should not be used for safety-critical decisions, enforcement, insurance,
  emergency situations, or anything requiring a verified location.

Good uses of the app include demonstrating classification, sequence logic,
confidence display, validation, and user-friendly machine-learning interaction.
It should not be treated as an actual parking tracker.

## Technical Stack

| Area | Tools and role |
| --- | --- |
| Python / data | Python, Pandas, NumPy, model preparation and validation |
| Machine learning | LightGBM classification models, exported to ONNX for inference |
| Backend | FastAPI, Pydantic, Uvicorn |
| Frontend | React, TypeScript, Vite, Tailwind CSS v4 |
| Visualisation | Custom timeline and prediction-card interface |
| Observability | Logfire |
| Deployment | Vercel (frontend); [confirm backend host] |

The deployed app separates the user interface from the prediction API. The
frontend collects trip details, the backend runs the model checks, and the UI
presents the result as an accessible parking-trail estimate.

## Repository Scope

This repo is a public documentation and project-overview companion for the
deployed app.

It includes:

- High-level project explanation.
- Methodology and modelling rationale.
- Data-source, assumption, and stack summaries.
- Public-facing interpretation notes.

It does not include:

- The full source implementation.
- Deployment configuration or secrets.
- Private environment variables or API keys.
- Model artefacts, if these are not intended for public release.
- Raw internal notes or unpublished implementation details.

That boundary is intentional: the repo is meant to make the project legible and
credible while keeping the implementation private for now.

## Suggested Demo Flow

1. Open the live app.
2. Keep the default starting location as Home, or change it before the first trip.
3. Add a few plausible recent trips.
4. Follow the trail.
5. Inspect the final Home vs Other result.
6. Open the technical details if you want to see the model checks.
7. Download the CSV if you want to inspect the trip-level prediction output.

## Links

- Live app: [wheresmycar.daguilardo.com](https://wheresmycar.daguilardo.com)
- GitHub: [DonovanAD](https://github.com/DonovanAD)
- LinkedIn: [Donovan Aguilar](https://www.linkedin.com/in/donovanad/)