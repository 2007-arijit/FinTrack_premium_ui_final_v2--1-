# PayShield

Scam-check module inside FinTrack. Local rules run in the browser, an optional Gemini review runs in a Supabase Edge Function, and every scan is stored per user.

## Where things live
| Part | File |
|---|---|
| Page (scanner, scan flow, AI status) | `src/pages/PayShield.tsx` |
| Rule engine and scan types (unchanged logic) | `src/lib/payshieldEngine.ts` |
| Result view, history, network illustration | `src/components/payshield/ScanResultView.tsx`, `ScanHistory.tsx`, `NetworkMap.tsx` |
| History query | `src/hooks/usePayShieldHistory.ts` |
| Score dial | `src/components/payshield/RiskGauge.tsx` |
| History chart | `src/components/payshield/ScanTrends.tsx` |
| Motion styles (`ps-reveal`, `ps-scan-bar`) | `src/index.css` |
| AI review function | `supabase/functions/payshield-analyze/index.ts` |
| Scan history table and RLS | `supabase/migrations/012_payshield_scan_history.sql` |
| Earlier local-only version (reference) | `src/pages/PayShield.local-backup.tsx` |

## Risk scoring
Each finding adds points: high 3, medium 2, note 0 (max 10). Score 0-1 is low, 2-4 is caution, 5 and above is high. The AI review can raise the rating but never lower it.

## Deploy the Edge Function (PowerShell, from the folder containing package.json)
```powershell
npx supabase functions deploy payshield-analyze
```
The key `GEMINI_API_KEY` is a Supabase secret. Never put it in `.env` or any `VITE_` variable.

## Changes in this version
- Rules added: debt or credit-report pressure, and web addresses written without `http` or `www`.
- Gemini model changed from `gemini-2.5-flash` to `gemini-3.8-flash` (the 2.5 model is closed to new users).
- Migration 012 copied into the migrations folder (safe to re-run).
- PayShield redesigned: dial for the score, per-finding severity rails, a "what to do next" section, history chart, confirmation before clearing history, keyboard focus styles, reduced-motion support.
- Notes moved from the project root into `docs/`.

## Known issues (not caused by PayShield)
- (Fixed) `SettleUpModal.tsx` undefined setter and the `usePushNotifications` type error.
