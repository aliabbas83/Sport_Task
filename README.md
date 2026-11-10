# Sports Quiz

A two-team mobile sports trivia game built with Expo (React Native), TypeScript and Expo Router.
On the welcome page, players pick a category: a preset such as Football or Formula 1, one of four
fresh ideas suggested by AI, or any topic they type (e.g. "NBA in the 90s"). Then two teams enter
their names, pick a difficulty (Easy, Medium or Hard), and take turns answering
10 fresh multiple-choice questions on that category, written by AI through
[OpenRouter](https://openrouter.ai) — 5 each. A correct
answer scores a point for the team whose turn it is. If the teams are tied after 10 questions, a
sudden-death tiebreaker gives each team one more question per round until one round ends with
different scores. The final page shows the winning team and the points it earned.

## Run

1. Copy `.env.example` to `.env.local` and set `OPENROUTER_API_KEY` (get one at
   https://openrouter.ai/keys). `.env.local` is git-ignored; never use an `EXPO_PUBLIC_` prefix
   for the key, or it ships inside the app.
2. Start the dev server (restart it after changing `.env.local`):

```bash
npm install
npx expo start          # add --tunnel if your phone can't connect
npm run tunnel          # tunnel mode on port 8082 that restarts itself if it crashes
```

If the OpenRouter account runs out of credit (HTTP 402), the server switches to a free model
automatically (slower). If the AI fails, the game offers offline questions.

Before finishing a change, run `npx tsc --noEmit` and `npx expo lint`.

The key is only used by the server route `src/app/api/questions+api.ts`. In development that runs
inside the Expo dev server on your computer. For a published app, deploy the API route
(e.g. EAS Hosting) and set the `origin` option of the `expo-router` plugin.

Scan the QR code with the Expo Go app (Android) or the Camera app (iOS).

## Structure

- `src/app/index.tsx` – welcome screen: title, how to play, Category button, Start
- `src/app/teams.tsx` – registration: the two team names (VS)
- `src/app/category.tsx` – category picker: presets, AI-suggested ideas (↻ for new ones), custom topic
- `src/app/difficulty.tsx` – difficulty screen (Easy / Medium / Hard)
- `src/app/game.tsx` – game screen (loading, error, playing)
- `src/app/result.tsx` – final page: winning team and its points, or a draw
- `src/app/api/questions+api.ts` – server API route: `POST /api/questions { amount, difficulty, category, avoid }`
- `src/app/api/categories+api.ts` – server API route: `POST /api/categories { avoid }` → 4 AI category ideas
- `src/server/openrouter.ts` – server-only OpenRouter helper: key, timeout, cancel, errors, JSON parsing
- `src/server/generateQuestions.ts` – question prompt, JSON schema, validation, shuffling, varied answers
- `src/server/suggestCategories.ts` – category-idea prompt and validation
- `src/api/questions.ts`, `src/api/categories.ts` – app clients for the API routes; both remember what
  was shown to avoid repeats
- `src/hooks/useQuiz.ts` – game state (load questions, turns, per-team scores, play again)
- `src/hooks/useCategoryIdeas.ts` – AI category ideas for the picker (load, refresh)
- `src/components/` – AppHeader, ScreenBackground, CategoryChoiceButton, CategoryButton, IdeaChip,
  TeamNameInput, TeamScoreboard, DifficultyButton, InfoPill, QuestionCard, AnswerButton,
  ProgressBar, NextButton, PrimaryButton, ResultCard
- `src/data/categories.ts` – preset categories and custom-topic validation (shared by app and server)
- `src/data/teams.ts` – team names, defaults and turn order
- `src/data/questions.ts` – `Question` / `Difficulty` types and offline fallback questions
- `src/constants/theme.ts` – colors and radii

## Handover

[projectDescription.md](projectDescription.md) has the full project history, design decisions, fixes,
steps for moving to new GitHub / Vercel / Claude accounts, and the complete source code.
