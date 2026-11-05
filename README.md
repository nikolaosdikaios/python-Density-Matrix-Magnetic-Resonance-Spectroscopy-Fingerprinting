
pyDMRSF simulates prostate ¹H magnetic resonance spectroscopy with the quantum density-matrix formalism. It designs multi-sequence acquisitions ("fingerprints") that make the metabolites you choose quantifiable, and fits spectra. The app is one file, `pyDMRSF` and needs no Python, no installation and no other files.

Every spectrum is simulated. pyDMRSF is not a validated clinical tool and must not be used for diagnosis.

1. What you need
It needs glibc 2.38 or later (for example Ubuntu 24.04, Debian 13, Fedora 39 or RHEL 10, or later). It includes the classic tools and the library libxcb-cursor.

2. Starting the app

Double-click `pyDMRSF`. A Terminal window opens first and shows the program's messages, and then the pyDMRSF window opens. Leave the Terminal window open while you work, because closing it quits pyDMRSF.

The app is not signed with an Apple developer certificate. To allow it, open Terminal (in
Applications › Utilities) and type this line, with a space at the end, but do not press Return yet:

xattr -dr com.apple.quarantine 

Then drag the file `pyDMRSF` from Finder onto the Terminal window, which adds its
path, and press Return. Nothing is printed when it works.

The Spectral Workbench opens on its Spectrum tab, with a simulated prostate
voxel. 

3. Using the app

The window has two tabs:

 Spectrum works on one simulated or loaded voxel: pre-processing, fitting,
  CRLBs, water-referenced concentrations and a report on spectral quality.
 MRF fingerprint has two views. Design designs the acquisitions for the
  metabolites you choose. Pipeline runs the analyses of the full
  simulation-and-fitting pipeline, including those of the paper, and compares
  results.

| Menu | Commands |
| --- | --- |
| File | Load spectrum… (⌘O), Export figure… (⌘S), Export data… (⌘⇧S), Quit (⌘Q) |
| Process | Auto-phase (⌘P), Fit spectrum (⌘R), Assess spectral quality… (⌘J), Reset pre-processing (⌘0) |
| Tools | MRF fingerprint designer (⌘G), MRSI spatial view… (⌘M), Send voxel to external script… (⌘E), Classic tools… (⌘⇧K) |
| View | Frame metabolites (⌘1), Full range, Toggle theme (⌘D) |
| Help | About pyDMRSF |

On Windows and Linux, the shortcuts use Ctrl instead of ⌘. View › Toggle theme
switches between the clinical, light and dark looks, and changes nothing in the
analysis.

Left: the Pipeline panel

    - Source: the sequence (PRESS, STEAM, LASER, semi-LASER, MEGA-PRESS) and its
      timing (TE, TR, flip angle; TM of STEAM, τ1 of PRESS, the editing of
      MEGA-PRESS), and the tissue, SNR, averages and B1 scale of the simulated voxel.
      Simulate makes a new voxel, and Load opens your own spectrum.
    - Pre-processing: eddy-current correction, frequency shift, B0 lineshape, HSVD
      water removal, apodization, zero-filling and phase (P0, and P1 about a pivot that
      you drag in the plot).
    - Shim / B0 & references: a B0 field map, and a water reference for the mM
      column.
    - Coils and Tissue composition: a phased-array combination, and the tissue and
      fluid fractions for the mM correction.
    - Basis set & fit: the metabolites of the fit, and a macromolecule baseline.
    - Residual: takes metabolites out of the displayed data, to show what each one
      leaves.

Centre: the spectrum with the fit (and each metabolite, if you like), the
  residual, and the FID.
Right: Metrics & metadata: the acquisition parameters, the SNR and linewidth,
  and the fit table, with the concentrations, CRLBs (amber above 20 %, red above
  50 %) and water-scaled mM.

Every change runs the chain again at once. Process › Fit spectrum fits the
spectrum. Process › Assess spectral quality says what the spectrum can support,
metabolite by metabolite.

Designing a fingerprint (MRF fingerprint › Design)


1. Metabolites & weights. Tick the metabolites in the model. To change a number,
   click it and type a new one. ↑ and ↓ step it by 0.1.

    - The weight (0–10) is how much the accuracy of that metabolite matters. A
      metabolite with weight 0 stays in the fit, but the design does not optimise for
      it.
    - Clinical weights sets the weights of the paper: citrate 3, choline 3,
      creatine 1, lactate 1, myo-inositol 0.4, taurine 0.4, and 0 for spermine and
      scyllo-inositol. Equal weights gives every ticked metabolite 1.
    - Add brings in another metabolite of the library.
    - The healthy and cancer concentrations (mM) are edited in the same way.

2. Sequences. The sequence families that the design may use, MEGA-PRESS editing,
   and asymmetric PRESS half-echoes.
3. Scan budget. The number of acquisitions, the averages of each, the longest scan
   time, the allowed TRs and flip angles (separated by commas), the largest number of
   identical acquisitions, and the shortest TE of your scanner.
4. Model & objective. The SNR, the tissues, the objective, the error model, the
   relaxation model, the baseline and lipid levels, and the effort of the search.
5. Design fingerprint. The design runs in the background, with a progress bar and
   Cancel. The first design simulates the candidate acquisitions (10–20 s). The
   search and the validation on held-out voxels then take from about 15 seconds to a
   minute, depending on the effort. A new design with other weights reuses the
   simulations.

The result has two parts:

 The Fingerprint card shows the number of acquisitions and the scan time, the
  objective against the best conventional PRESS acquisition, and the held-out Monte
  Carlo check. It has the buttons Open acquisition in Spectrum view, Save
  JSON… and Save CSV….
 The tabs under it:

    - Schedule: the acquisitions, in the order of acquisition. Double-click one to
      open it in the Spectrum tab;
    - Metabolites: for each metabolite, its weight, its expected error against the
      best conventional PRESS, and what makes up the error;
    - Fingerprints: the spectrum of each metabolite in every acquisition, which is
      what the joint fit sees;
    - Similarity: how much the metabolites' fingerprints resemble each other, next
      to the best conventional schedule;
    - Errors: the expected error of each weighted metabolite, against the best
      conventional PRESS;
    - Validation: the errors on simulated voxels that the design never saw, for the
      fingerprint and the conventional schedules, with 95 % intervals.

Assess schedule… scores a saved schedule, or your own protocol, in the same way.
If you change a setting after a design, a note above the result reminds you to design
again.

3.4 Running analyses (MRF fingerprint › Pipeline)


The left column, from the top:

1. Results folder: where the analyses write their files. The default is
   `pyDMRSF_results` in your home folder. Browse… chooses another folder, and
   Open shows it in Finder or Explorer.
2. Analyses: tick what to run.

    - Time is a rough figure for one processor core.
    - Results shows what the folder already holds: ✓ results, q a quick run,
      ✗ failed, – none.
    - The tooltip of an analysis says what it does.

3. Run settings:

    - Fields: 3 T, 7 T, or others.
    - Size: Full, or Quick, a smoke test whose numbers are not full results.
    - Parallel analyses: how many analyses run at once, one core each.
    - Also run what writes missing inputs.
    - Keep results already in the folder: resumes a run, and keeps imported results.

4. Model and fit: every switch of the simulation and the fit. They start at the
   defaults of the pipeline, which are the settings of the paper. A changed value is
   shown in bold, and Pipeline defaults resets them all.
5. Inputs: the schedules that the evaluations compare with conventional PRESS.
   These are the schedules a search wrote into the folder, those imported from another
   results folder (Import from folder…), a schedule file (Add schedule file…),
   and the design of the Design view (Add the designed fingerprint).
6. Run: the plan (how many analyses, and for about how long), Run analyses,
   Cancel, and Build tables, which makes the LaTeX tables of the paper from the
   results.

The right side has three tabs:

 Results: each analysis, with its report, figures and tables;
 Compare: the results folder against another, number by number;
 Log: the output of the analyses. Each analysis also writes `logs/<analysis>.log`
  in the results folder.

Two analyses need PyTorch, which the app does not include: Gradient versus random
search and Analytic surrogate. The PyTorch part of Physics checks is left out
too. The plan skips the two analyses, and their results can be imported from the
results folder that comes with the app (section 4).

Other tools

 MRSI spatial view (Tools › MRSI spatial view…): a digital prostate phantom with
  the acquisition grid. Click a voxel to see its spectrum, with the lipid bleed of the
  point-spread function. Send voxel → Workbench opens that spectrum in the
  Spectrum tab.
 Classic tools (Tools › Classic tools…): the guided tabbed application, with
  editors for custom spin systems, concentrations and relaxation times. The paragraph
  about this copy, in section 1, says whether it includes them.
 Send voxel to external script (Tools): runs your own Python script on the
  current voxel, for example a PyTorch denoiser, and overlays the spectrum it returns.
  It needs Python 3 with NumPy on the computer, as `python3` (on Windows, `py` or
  `python`). The script gets the paths of an input and an output `.npz` file: it
  reads `ppm` and `spec` from the first and writes at least `spec` into the second.

3.6 Where files go

 The Pipeline view writes into its results folder.
 Exports go where you choose.
 The classic tools write into a folder `results` in the folder that pyDMRSF was
  started from. When you double-click pyDMRSF on a Mac, that is your home folder.
  File › Set results folder… changes it.
 The app never changes its own files. This includes the results of the paper's run
  that it contains (`run_2026-10-20`, section 4).

---

4. Regenerating the results of the paper

The steps below run every analysis behind the tables, figures and numbers of the paper
and its supplement, with the settings of the paper, which are the defaults of the
Pipeline view. They then compare the results with the run of 19–20 October 2026, which
comes with the app as the results folder run_2026-10-20.

 Time. About an hour of computer time: 32 minutes on a two-core computer that
  runs two analyses at once.
 Searches. The three schedule searches would take about 12 hours more. The steps
  therefore take their results from the bundled run. To repeat the searches, skip
  step 5.
 PyTorch. The two analyses that need PyTorch are not in the app (section 3.4).
  Their results are also taken from the bundled run. The accuracy of the
  differentiable simulator (Section 2.5 of the paper) needs PyTorch too, and the Python
  version of pyDMRSF.

Step by step

1. Start pyDMRSF (section 2). The window opens on the Spectrum tab.
2. Open the Pipeline view. Click the MRF fingerprint tab at the top of the
   window, then Pipeline in the bar under it.
3. Choose a results folder. In the Results folder box at the top left, click
   Browse…. Make a new, empty folder (for example `pyDMRSF_results/paper` in your
   home folder) and choose it.
4. Check the settings.

    - Model and fit: click Pipeline defaults. These are the settings of the
      paper.
    - Run settings: under Fields, tick 3 T and 7 T. Set Size to
      Full, and Parallel analyses to the number of processor cores minus one.
    - Leave Also run what writes missing inputs and Keep results already in the
      folder ticked.

5. Take the schedules of the paper. Under Inputs, click Import from
   folder… and choose run_2026-10-20 (bundled).

    - The dialog lists the files of that run, each with the analysis that writes it. It
      has already selected what you need: the results of the three schedule searches,
      and those of the two analyses that need PyTorch.
    - Click Import. The Inputs list now shows the two searched schedules at
      3 T and at 7 T, and the Analyses list shows ✓ next to the searches.


6. Select every analysis. Under Analyses, click Select all. The line under
   Run shows the plan: how many analyses will run and for about how long, which
   imported results it keeps, and which analyses it skips (those that need PyTorch).
7. Run. Click Run analyses, and Yes if it asks you to confirm a long run.

    - The progress bar and the line under it show what is running. The Log tab, on
      the right, shows the output of every analysis.
    - Keep pyDMRSF open and the computer awake until the run ends. On a Mac, for
      example, run `caffeinate -d` in a Terminal window. Closing pyDMRSF stops the run,
      after asking.
    - Cancel stops the run. Clicking Run analyses again later resumes it, and
      keeps the analyses already done.

8. The tables. When the run ends, the LaTeX tables are written into the results
   folder: `main_tables.tex` (Tables 3–5) and `supplementary_tables.tex` (Tables S2, S3
   and S5–S14). Build tables writes them again.
9. Look at the results. The Results tab lists every analysis that has results.
   Under each one are its Report, its Figures and the Tables built from it.
   Click one to show it. Open file opens the file itself, for example a figure at
   full size. [Where each result is](#where-each-result-is) lists where every table
   and figure of the paper is.
10. Check them against the bundled run. Open the Compare tab, choose
    run_2026-10-20 (bundled) and click Compare.

    - Every file that is in both folders reads identical or matches. This was
      checked with the app for Linux. On another system, the last digits of a few
      numbers can come out differently, because the processor and the mathematical
      libraries round differently.
    - The timings of Computation time depend on the computer, and are not compared.
    - Some files are in one folder only. Model inputs and Physics checks were added
      to the pipeline after the bundled run, and the bundled run holds a few files that
      no analysis writes any more.
    - In the Results tab, the heading of each table and report now says whether it
      is the same as in the bundled run, or how many cells differ. The cells that
      differ are highlighted.

