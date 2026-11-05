# Music taste and political ideology: replicating Myers (2019) with Machine 2

Main takeaways from `music_politics_replication.ipynb` (run 2026-10-03). Paper: Myers (2019), *The songs of my
people*, NYU honors thesis (`paper_bin/`).

## Bottom line

Music ratings do carry information about ideology, and the same songs split liberals from non-liberals in both
datasets. The paper's headline accuracy also reproduces: 70.5% vs. 71.5%. But that number was computed on the same
people the classifier was fit on. On held-out participants the gain over always guessing "liberal" is about 4 points
(AUC 0.69). Much of that gain comes from separating left-leaning people from those who say they don't care about
politics, not from separating left from right. Most of the paper's genre-level claims do not replicate.

## Key numbers

| | Paper | Machine 2 |
|---|---|---|
| Participants analysed | 576 | 752 |
| Liberal / non-liberal | 346 / 230 (60.1% liberal) | 480 / 272 (63.8% liberal) |
| Click-through exclusions | 49 of 625 (7.8%) | 17 of 769 (2.2%) |
| Songs | 260; each person rated a random subset | 320 core clips, all rated by everyone |
| **In-sample reclassification accuracy** (paper's method) | **71.5%** | **70.5%** |
| Majority baseline (always "liberal") | 60.1% | 63.8% |
| Gain over baseline | +11.4 pts | +6.6 pts |
| Same method on shuffled labels (mean / 95th pct) | not reported | 65.4% / 67.6% |
| **Held-out accuracy** (5-fold × 20) | not reported | **68.2%** (range 67.0–69.1%) |
| Held-out balanced accuracy | not reported | 61.7% |
| **Held-out AUC** | not reported | **0.689** (p = 0.001 vs. shuffled labels) |
| Song-level agreement with the paper | — | r = 0.62 over 132 shared songs |

"Liberal" = Liberal + Progressive + Socialist. "Non-liberal" = everyone else, including "I don't care", following
the paper's rule that everyone not liberal is non-liberal.

## Takeaways

### 1. The paper's 71.5% is optimistic, but there is a real signal underneath
- In the paper, the song weights and the cutoff were estimated on the same people being classified. With shuffled
  labels, where music carries no information about ideology, that procedure still scores 65.4% on average, about 1.6
  points above the baseline from overfitting alone.
- On participants left out of fitting, accuracy is 68.2%: +4.4 points over the baseline, and stable across the 20
  data splits. The AUC of 0.69 is far outside the shuffled-label null (all 1,000 shuffles ≤ 0.58).
- The best-accuracy cutoff calls about 76% of people "liberal". It finds non-liberals poorly: 114 of 272 were caught
  in-sample. Accuracy is therefore a weak summary here; AUC or balanced accuracy is more informative.

### 2. The song-level result replicates best
- The paper's per-song differences agree with Machine 2's across the 132 shared songs: r = 0.62, and 73% have the same
  sign. Strong examples are Hypnotize (+1.01 in the paper, +0.76 here), Smells Like Teen Spirit (+0.89, +0.76),
  It Was A Good Day, Baby Got Back, and Texas Tornado (−0.75, −0.82).
- Across all 320 clips, 143 differ between groups at FDR q < .05, and 51 exceed a family-wise permutation threshold
  (|diff| > 0.46).
- By genre, rap (+0.32) and R&B/soul (+0.29) lean liberal, and country (−0.29) leans non-liberal. Classical is the most
  neutral (−0.04), consistent with the paper's note that instrumental classical and jazz polarize least.
- The clips most liked by non-liberals are country (Texas Tornado, Independence Day, Sara Evans, Randy Travis) and
  recent mainstream pop (Shape of You, Perfect, Closer, Despacito, Happy). The clips most liked by liberals are older
  rap, R&B, rock and classic pop (Bootylicious, Smells Like Teen Spirit, Hypnotize, American Idiot, Footloose).
- 222 of 320 clips lean liberal, so part of the index may reflect a general tendency of left-leaning participants to
  rate music higher, not taste in specific songs.

### 3. The paper's genre-level claims mostly do not replicate
| Paper's claim | Machine 2 |
|---|---|
| Liberals report listening to more genres (3.6 vs. 3.1 of 8) | No difference (3.9 / 3.8 / 3.8) |
| Liberals report less country listening, yet *like* country clips more than conservatives | They do report less country (14% vs. ~20%), but they also like country clips **least** (3.45 vs. 3.85 moderate, 3.82 conservative). Self-report and lab ratings agree |
| Self-reported listening is not correlated with in-lab liking | Clearly correlated in every genre: r = 0.23 (pop) to 0.47 (jazz), all p < .001 |
| Liberals report much more R&B listening (57% vs. 21%) | Not replicated (65% / 59% / 68%) |

The conservative group is small in both studies (22 in the paper; 41 conservatives, libertarians and alt-right here),
so the three-group comparisons are noisy. Separately, the paper's familiarity figure (Fig 4) duplicates its appraisal
figure (Fig 3), so its familiarity results cannot be checked.

### 4. The result depends on who counts as "non-liberal"
| Grouping | n | Baseline | Held-out accuracy | Held-out AUC |
|---|---|---|---|---|
| **Main:** left vs. everyone else | 752 | 63.8% | 68.2% | **0.689** |
| Only "Liberal" vs. everyone else | 752 | 55.7% | 57.8% | 0.615 |
| Left vs. everyone else, without "I don't care" | 623 | 77.0% | 76.4% | 0.629 |
| Left vs. right (Conservative / Libertarian / Alt-Right) | 521 | 92.1% | 91.9% | 0.610 |

- All four AUCs beat shuffled labels (p ≤ .018), so music ranks people by ideology above chance in every grouping.
- Removing the 129 "I don't care" respondents drops the AUC from 0.69 to 0.63. With them removed, the classifier no
  longer beats the majority baseline on accuracy, and in the left-vs-right contrast it doesn't either.
- So a good part of what music predicts here is *political engagement or identity* (left-leaning vs. apolitical),
  not left vs. right.

## Caveats
- The Machine 2 sample leans strongly left, and its ideology options (Progressive, Socialist, I don't care, …) differ
  from the paper's, so the groupings are not identical.
- Ideology is likely confounded with demographics that music predicts well; in the binary notebook, gender and race
  were the most predictable targets. This replication does not control for them, so part of the music–ideology link
  may run through demographics.
- The click-through check had to be adapted: Machine 2 never repeats a song, so consistency was measured with odd vs.
  even trial genre means. It excluded 2.2% of participants, vs. 7.8% in the paper.

## Files
- `music_politics_replication.ipynb`: full analysis
- `music_politics_song_differences.csv`: per-clip liberal vs. non-liberal means, differences and tests (320 clips)
- `music_politics_reclassification.csv`: reclassification results for the four groupings
- `paper_bin/myers2019_fig5_song_differences.csv`: the paper's 260 per-song differences, transcribed from its Fig 5,
  matched to Machine 2 clip IDs
