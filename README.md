# pyDMRSF — the standalone app

pyDMRSF simulates prostate ¹H magnetic resonance spectroscopy with the quantum
density-matrix formalism. It designs multi-sequence acquisitions ("fingerprints")
that make the metabolites you choose quantifiable, and fits spectra. The app is one
file, `pyDMRSF` (`pyDMRSF.exe` on Windows), which holds the complete program. It needs
no Python, no installation and no other files.

> **Research tool.** Every spectrum is simulated. pyDMRSF is not a validated clinical
> instrument and must not be used for diagnosis.

**Contents**

1. [What you need](#1-what-you-need)
2. [Starting the app](#2-starting-the-app)
3. [Using the app](#3-using-the-app)
4. [Regenerating the results of the paper](#4-regenerating-the-results-of-the-paper)
5. [The command line](#5-the-command-line)
6. [Troubleshooting](#6-troubleshooting)
7. [More documentation](#7-more-documentation)

---

## 1. What you need

**This copy** is pyDMRSF 1.5.2 for **Linux on x86-64**, built on 3 November 2026 on Ubuntu 24.04.5 LTS. It needs **glibc 2.38 or later** (for example Ubuntu 24.04, Debian 13, Fedora 39 or RHEL 10, or later), and takes 159 MB. It includes the classic tools and the library libxcb-cursor.

| | |
| --- | --- |
| Computer | The kind this copy was built for, as the paragraph above says: a Mac (Intel or Apple silicon), a 64-bit Windows PC, or a 64-bit Linux PC. |
| Operating system | The version given above, or a later one. |
| Python | Not needed: it is inside the app. |
| Screen | 1280 × 800 or larger. |
| Memory | 2 GB free. The Pipeline view runs every analysis in its own process, which needs about 0.2 GB more. |
| Disk | The size given above for the file, about 400 MB of temporary space while it runs (twice that per analysis running at once), plus the results you make. All the analyses of the paper write less than 100 MB. |
| Internet | Not needed. |
| Linux only | A graphical desktop, and the system's OpenGL and X11 libraries (`libgl1`, `libegl1` and `libxcb1` on Debian and Ubuntu). Desktop installations have them. |

You can put the file anywhere, for example into Applications on a Mac, and give it to
anyone as it is. When it starts, it unpacks itself into a temporary folder, which it
removes when it quits.

---

## 2. Starting the app

### On a Mac

1. **The first time, allow the app to run**, as described below.
2. **Double-click `pyDMRSF`.** A Terminal window opens first and shows
   the program's messages, and then the pyDMRSF window opens. Leave the Terminal
   window open while you work, because closing it quits pyDMRSF.

**Allowing the app to run.** The app is not signed with an Apple developer
certificate. When it comes from the internet, an e-mail or AirDrop, macOS therefore
blocks it. To allow it, open **Terminal** (in
Applications › Utilities) and type this line, with a space at the end, but do not
press Return yet:

```
xattr -dr com.apple.quarantine 
```

Then drag the file `pyDMRSF` from Finder onto the Terminal window, which adds its
path, and press Return. Nothing is printed when it works.

Without this step, macOS says that pyDMRSF "cannot be opened" or "is damaged". In that
case you can also allow it in **System Settings › Privacy & Security**: under
*Security*, click **Open Anyway** next to the message about pyDMRSF.

### On Windows

1. **Double-click `pyDMRSF.exe`.** If Windows says "Windows protected
   your PC", click **More info**, then **Run anyway**: the app is not signed. A
   console window opens with the program's messages, and then the pyDMRSF window
   opens. Leave the console window open while you work.

Some antivirus programs distrust unsigned programs. If yours quarantines a file of the
app, restore it and allow `pyDMRSF.exe`.

### On Linux

```bash
chmod +x pyDMRSF      # once, if it is not executable after the download
./pyDMRSF             # opens the window; its messages appear in the terminal
```

A file manager that runs programs can also start it with a double-click.

### What you should see

The **Spectral Workbench** opens on its **Spectrum** tab, with a simulated prostate
voxel. **Process › Fit spectrum** fits it.

### Checking the app

In a terminal, run the program with an option. Type the path of the program and then
the option, separated by a space. The quickest way to type the path is to drag
`pyDMRSF` (`pyDMRSF.exe`) from Finder or Explorer into the terminal window.

```bash
./pyDMRSF --version      # pyDMRSF and its version
./pyDMRSF --selftest     # the engine, the analyses and the window; ends with SELF-TEST PASSED
```

The self-test takes less than a minute and opens no window.

---

## 3. Using the app

### 3.1 The window

The window has two tabs:

* **Spectrum** works on one simulated or loaded voxel: pre-processing, fitting,
  CRLBs, water-referenced concentrations and a report on spectral quality.
* **MRF fingerprint** has two views. **Design** designs the acquisitions for the
  metabolites you choose. **Pipeline** runs the analyses of the full
  simulation-and-fitting pipeline, including those of the paper, and compares
  results.

| Menu | Commands |
| --- | --- |
| File | Load spectrum… (⌘O), Export figure… (⌘S), Export data… (⌘⇧S), Quit (⌘Q) |
| Process | Auto-phase (⌘P), Fit spectrum (⌘R), Assess spectral quality… (⌘J), Reset pre-processing (⌘0) |
| Tools | MRF fingerprint designer (⌘G), MRSI spatial view… (⌘M), Send voxel to external script… (⌘E), Classic tools… (⌘⇧K) |
| View | Frame metabolites (⌘1), Full range, Toggle theme (⌘D) |
| Help | About pyDMRSF |

On Windows and Linux, the shortcuts use Ctrl instead of ⌘. **View › Toggle theme**
switches between the clinical, light and dark looks, and changes nothing in the
analysis.

### 3.2 The Spectrum tab

* **Left: the Pipeline panel.**

    - *Source*: the sequence (PRESS, STEAM, LASER, semi-LASER, MEGA-PRESS) and its
      timing (TE, TR, flip angle; TM of STEAM, τ1 of PRESS, the editing of
      MEGA-PRESS), and the tissue, SNR, averages and B1 scale of the simulated voxel.
      **Simulate** makes a new voxel, and **Load…** opens your own spectrum.
    - *Pre-processing*: eddy-current correction, frequency shift, B0 lineshape, HSVD
      water removal, apodization, zero-filling and phase (P0, and P1 about a pivot that
      you drag in the plot).
    - *Shim / B0 & references*: a B0 field map, and a water reference for the mM
      column.
    - *Coils* and *Tissue composition*: a phased-array combination, and the tissue and
      fluid fractions for the mM correction.
    - *Basis set & fit*: the metabolites of the fit, and a macromolecule baseline.
    - *Residual*: takes metabolites out of the displayed data, to show what each one
      leaves.

* **Centre**: the spectrum with the fit (and each metabolite, if you like), the
  residual, and the FID.
* **Right: Metrics & metadata**: the acquisition parameters, the SNR and linewidth,
  and the fit table, with the concentrations, CRLBs (amber above 20 %, red above
  50 %) and water-scaled mM.

Every change runs the chain again at once. **Process › Fit spectrum** fits the
spectrum. **Process › Assess spectral quality…** says what the spectrum can support,
metabolite by metabolite.

**Your own spectrum.** **File › Load spectrum…** reads a CSV, TSV or text file with
the columns `ppm,real,imag` (or `ppm,intensity`). **File › Export figure…**
writes PDF, SVG, EPS or 300-dpi PNG, and **File › Export data…** writes NPZ, CSV or
NPY.

### 3.3 Designing a fingerprint (MRF fingerprint › Design)


1. **Metabolites & weights.** Tick the metabolites in the model. To change a number,
   click it and type a new one. ↑ and ↓ step it by 0.1.

    - The **weight** (0–10) is how much the accuracy of that metabolite matters. A
      metabolite with weight 0 stays in the fit, but the design does not optimise for
      it.
    - **Clinical weights** sets the weights of the paper: citrate 3, choline 3,
      creatine 1, lactate 1, myo-inositol 0.4, taurine 0.4, and 0 for spermine and
      scyllo-inositol. **Equal weights** gives every ticked metabolite 1.
    - **Add** brings in another metabolite of the library.
    - The healthy and cancer concentrations (mM) are edited in the same way.

2. **Sequences.** The sequence families that the design may use, MEGA-PRESS editing,
   and asymmetric PRESS half-echoes.
3. **Scan budget.** The number of acquisitions, the averages of each, the longest scan
   time, the allowed TRs and flip angles (separated by commas), the largest number of
   identical acquisitions, and the shortest TE of your scanner.
4. **Model & objective.** The SNR, the tissues, the objective, the error model, the
   relaxation model, the baseline and lipid levels, and the effort of the search.
5. **Design fingerprint.** The design runs in the background, with a progress bar and
   **Cancel**. The first design simulates the candidate acquisitions (10–20 s). The
   search and the validation on held-out voxels then take from about 15 seconds to a
   minute, depending on the effort. A new design with other weights reuses the
   simulations.

The result has two parts:

* The **Fingerprint** card shows the number of acquisitions and the scan time, the
  objective against the best conventional PRESS acquisition, and the held-out Monte
  Carlo check. It has the buttons **Open acquisition in Spectrum view**, **Save
  JSON…** and **Save CSV…**.
* The tabs under it:

    - **Schedule**: the acquisitions, in the order of acquisition. Double-click one to
      open it in the Spectrum tab;
    - **Metabolites**: for each metabolite, its weight, its expected error against the
      best conventional PRESS, and what makes up the error;
    - **Fingerprints**: the spectrum of each metabolite in every acquisition, which is
      what the joint fit sees;
    - **Similarity**: how much the metabolites' fingerprints resemble each other, next
      to the best conventional schedule;
    - **Errors**: the expected error of each weighted metabolite, against the best
      conventional PRESS;
    - **Validation**: the errors on simulated voxels that the design never saw, for the
      fingerprint and the conventional schedules, with 95 % intervals.

**Assess schedule…** scores a saved schedule, or your own protocol, in the same way.
If you change a setting after a design, a note above the result reminds you to design
again.

### 3.4 Running analyses (MRF fingerprint › Pipeline)


The left column, from the top:

1. **Results folder**: where the analyses write their files. The default is
   `pyDMRSF_results` in your home folder. **Browse…** chooses another folder, and
   **Open** shows it in Finder or Explorer.
2. **Analyses**: tick what to run.

    - *Time* is a rough figure for one processor core.
    - *Results* shows what the folder already holds: ✓ results, q a quick run,
      ✗ failed, – none.
    - The tooltip of an analysis says what it does.

3. **Run settings**:

    - *Fields*: 3 T, 7 T, or others.
    - *Size*: *Full*, or *Quick*, a smoke test whose numbers are not full results.
    - *Parallel analyses*: how many analyses run at once, one core each.
    - *Also run what writes missing inputs*.
    - *Keep results already in the folder*: resumes a run, and keeps imported results.

4. **Model and fit**: every switch of the simulation and the fit. They start at the
   defaults of the pipeline, which are the settings of the paper. A changed value is
   shown in bold, and **Pipeline defaults** resets them all.
5. **Inputs**: the schedules that the evaluations compare with conventional PRESS.
   These are the schedules a search wrote into the folder, those imported from another
   results folder (**Import from folder…**), a schedule file (**Add schedule file…**),
   and the design of the Design view (**Add the designed fingerprint**).
6. **Run**: the plan (how many analyses, and for about how long), **Run analyses**,
   **Cancel**, and **Build tables**, which makes the LaTeX tables of the paper from the
   results.

The right side has three tabs:

* **Results**: each analysis, with its report, figures and tables;
* **Compare**: the results folder against another, number by number;
* **Log**: the output of the analyses. Each analysis also writes `logs/<analysis>.log`
  in the results folder.

Two analyses need PyTorch, which the app does not include: *Gradient versus random
search* and *Analytic surrogate*. The PyTorch part of *Physics checks* is left out
too. The plan skips the two analyses, and their results can be imported from the
results folder that comes with the app (section 4).

### 3.5 Other tools

* **MRSI spatial view** (Tools › MRSI spatial view…): a digital prostate phantom with
  the acquisition grid. Click a voxel to see its spectrum, with the lipid bleed of the
  point-spread function. **Send voxel → Workbench** opens that spectrum in the
  Spectrum tab.
* **Classic tools** (Tools › Classic tools…): the guided tabbed application, with
  editors for custom spin systems, concentrations and relaxation times. The paragraph
  about this copy, in section 1, says whether it includes them.
* **Send voxel to external script** (Tools): runs your own Python script on the
  current voxel, for example a PyTorch denoiser, and overlays the spectrum it returns.
  It needs Python 3 with NumPy on the computer, as `python3` (on Windows, `py` or
  `python`). The script gets the paths of an input and an output `.npz` file: it
  reads `ppm` and `spec` from the first and writes at least `spec` into the second.

### 3.6 Where files go

* The Pipeline view writes into its results folder.
* Exports go where you choose.
* The classic tools write into a folder `results` in the folder that pyDMRSF was
  started from. When you double-click pyDMRSF on a Mac, that is your home folder.
  **File › Set results folder…** changes it.
* The app never changes its own files. This includes the results of the paper's run
  that it contains (`run_2026-10-20`, section 4).

---

## 4. Regenerating the results of the paper

The steps below run every analysis behind the tables, figures and numbers of the paper
and its supplement, with the settings of the paper, which are the defaults of the
Pipeline view. They then compare the results with the run of 19–20 October 2026, which
comes with the app as the results folder **run_2026-10-20**.

* **Time.** About an hour of computer time: 32 minutes on a two-core computer that
  runs two analyses at once.
* **Searches.** The three schedule searches would take about 12 hours more. The steps
  therefore take their results from the bundled run. To repeat the searches, skip
  step 5.
* **PyTorch.** The two analyses that need PyTorch are not in the app (section 3.4).
  Their results are also taken from the bundled run. The accuracy of the
  differentiable simulator (Section 2.5 of the paper) needs PyTorch too, and the Python
  version of pyDMRSF.

### Step by step

1. **Start pyDMRSF** (section 2). The window opens on the **Spectrum** tab.
2. **Open the Pipeline view.** Click the **MRF fingerprint** tab at the top of the
   window, then **Pipeline** in the bar under it.
3. **Choose a results folder.** In the **Results folder** box at the top left, click
   **Browse…**. Make a new, empty folder (for example `pyDMRSF_results/paper` in your
   home folder) and choose it.
4. **Check the settings.**

    - **Model and fit**: click **Pipeline defaults**. These are the settings of the
      paper.
    - **Run settings**: under *Fields*, tick **3 T** and **7 T**. Set *Size* to
      **Full**, and *Parallel analyses* to the number of processor cores minus one.
    - Leave **Also run what writes missing inputs** and **Keep results already in the
      folder** ticked.

5. **Take the schedules of the paper.** Under **Inputs**, click **Import from
   folder…** and choose **run_2026-10-20 (bundled)**.

    - The dialog lists the files of that run, each with the analysis that writes it. It
      has already selected what you need: the results of the three schedule searches,
      and those of the two analyses that need PyTorch.
    - Click **Import**. The **Inputs** list now shows the two searched schedules at
      3 T and at 7 T, and the **Analyses** list shows ✓ next to the searches.


6. **Select every analysis.** Under **Analyses**, click **Select all**. The line under
   **Run** shows the plan: how many analyses will run and for about how long, which
   imported results it keeps, and which analyses it skips (those that need PyTorch).
7. **Run.** Click **Run analyses**, and **Yes** if it asks you to confirm a long run.

    - The progress bar and the line under it show what is running. The **Log** tab, on
      the right, shows the output of every analysis.
    - Keep pyDMRSF open and the computer awake until the run ends. On a Mac, for
      example, run `caffeinate -d` in a Terminal window. Closing pyDMRSF stops the run,
      after asking.
    - **Cancel** stops the run. Clicking **Run analyses** again later resumes it, and
      keeps the analyses already done.

8. **The tables.** When the run ends, the LaTeX tables are written into the results
   folder: `main_tables.tex` (Tables 3–5) and `supplementary_tables.tex` (Tables S2, S3
   and S5–S14). **Build tables** writes them again.
9. **Look at the results.** The **Results** tab lists every analysis that has results.
   Under each one are its *Report*, its *Figures* and the *Tables* built from it.
   Click one to show it. **Open file** opens the file itself, for example a figure at
   full size. [Where each result is](#where-each-result-is) lists where every table
   and figure of the paper is.
10. **Check them against the bundled run.** Open the **Compare** tab, choose
    **run_2026-10-20 (bundled)** and click **Compare**.

    - Every file that is in both folders reads *identical* or *matches*. This was
      checked with the app for Linux. On another system, the last digits of a few
      numbers can come out differently, because the processor and the mathematical
      libraries round differently.
    - The timings of *Computation time* depend on the computer, and are not compared.
    - Some files are in one folder only. *Model inputs* and *Physics checks* were added
      to the pipeline after the bundled run, and the bundled run holds a few files that
      no analysis writes any more.
    - In the **Results** tab, the heading of each table and report now says whether it
      is the same as in the bundled run, or how many cells differ. The cells that
      differ are highlighted.



### Where each result is

In the **Results** tab, a result is an item under its analysis: **analysis** › item.
A name in brackets, such as [`baseline`], is a block of `main_tables.tex` (Tables
3–5) or of `supplementary_tables.tex` (the S tables), between the lines
`%% BEGIN AUTO:baseline` and `%% END AUTO:baseline`.

| Paper | In the Results tab | File in the results folder |
| --- | --- | --- |
| Table 1 | **Model inputs** › Report | `tab00_model_inputs.md` |
| Table 2 (the acquisitions compared) | **Whole-panel evaluation (3 T)** › Report; **Whole-panel schedule search (3 T)** › Searched schedules | `tab20_panel_statistics.md`; [`schedules`] |
| Table 3 | **Conditioning versus ridge** › Conditioning and errors of three acquisitions | [`baseline`] |
| Table 4 | **Whole-panel evaluation (3 T)** › Whole-panel errors at 3 T (both evaluation sets) | [`wholepanel3T`] |
| Table 5 | **Whole-panel evaluation (7 T)** › Whole-panel errors at 7 T (both evaluation sets) | [`wholepanel7T`] |
| Fig. 1 | **Anatomical phantoms** › Figure | `fig09_anatomical_phantom.png` |
| Fig. 2 | **Spectra of the patient phantoms** › Figure | `fig11_zonal_spectra.png` |
| Fig. 3 | **Direct schedule search** › Figure | `fig02_pinn.png` |
| Section 2.5 | **Physics checks** › Report (the accuracy of the differentiable simulator needs PyTorch, which the app does not include) | `tab24_quoted_numbers.md` |
| Section 3.1 | **Family ablation and kappa** › Report (tab01_ablation.md); **Conditioning versus ridge** › Report | `tab01_ablation.md`, `tab22_conditioning_sensitivity.md` |
| Section 3.2 | **Spin dynamics by sequence** › Report; **Signal at the echo** › Report | `tab05_mechanism.md`, `tab06_efficiency.md` |
| Section 3.3 | **Direct schedule search** › Report; **Analytic surrogate** › Report | `tab02_optimizer.md`, `tab03_surrogate.md` |
| Sections 3.4–3.6 | **Whole-panel evaluation (3 T)** and **(7 T)** › Report, and › Paired differences of the panel error | `tab20_panel_statistics[_7T].md`; [`paired`] |
| Section 3.7 | **Gradient versus random search** › Report | `tab18_gradient_control.md` |
| Section 3.8 | **Per-voxel perturbations** › Report; **Extreme artifacts** › Extreme artifacts; **MRSI lipid bleed** › Report | `tab10_all_ci.md`, `stress_test.json`, `tab08_ovs.md` |
| Section 3.9 | **Post-hoc spectral encoding** › Report | `tab23_spectral_encoding.md` |
| Section 3.10 | **Computation time** › Report (tab19_complexity.md): the timings of this computer | `tab19_complexity.md` |
| Section 4 (limitations) | **Physics checks** › Report | `tab24_quoted_numbers.md` |
| Table S1 | **Model inputs** › Report | `tab00_model_inputs.md` |
| Table S2 | **Physics checks** › Citrate pulse-acquire lines | [`citlines`] |
| Table S3 | **Whole-panel evaluation (3 T)** › Schedule-selection methods | [`optobj`] |
| Table S4 | **Model inputs** › Report | `tab00_model_inputs.md` |
| Table S5 | **Conditioning versus ridge** › Conditioning versus the fit ridge | [`condridge`] |
| Table S6 | **Whole-panel evaluation (3 T)** and **(7 T)** › Whole-panel errors on each evaluation set (a), and › Paired differences of every metabolite (b) | [`panelci3T`], [`panelci7T`], [`pairedmet`] |
| Table S7 | **Whole-panel schedule search (3 T)** › Searched schedules | [`schedules`] |
| Table S8 | **Fit sensitivity (3 T)** › Noise definition, relaxation variability and relaxation prior (a), and › Errors without relaxation variability (b) | [`noise`], [`relaxvar`] |
| Table S9 | **Fit sensitivity (3 T)** › Sensitivity to the fit ridge | [`ridge`] |
| Table S10 | **Fit sensitivity (3 T)** › Transmit-field error | [`b1`] |
| Table S11 | **Dedicated acquisitions** › Dedicated acquisitions | [`ceilings`] |
| Table S12 | **Gradient versus random search** › Gradient versus random echo times | [`gradient`] |
| Table S13 | **Per-voxel perturbations** › Per-voxel perturbations (a); **Extreme artifacts** › Extreme artifacts (b); **MRSI lipid bleed** › Lipid bleed in the MRSI model (c) | [`physics`], [`stress`], [`ovs`] |
| Table S14 | **Post-hoc spectral encoding** › Post-hoc spectral encoding | [`encoding`] |
| Fig. S1 | **Spin dynamics by sequence** › Figure | `fig05_quantum_mechanism.png` |
| Fig. S2 | **Signal at the echo** › Figure | `fig06_coherence_efficiency.png` |
| Fig. S3 | **Shaped refocusing** › Figure | `fig07_shaped_refocusing.png` |
| Graphical abstract | **Summary figure** › Figure | `graphical_abstract.png` (`.pdf`, `.tiff`) |

The tables built at both fields (Tables S6 and S8–S10) appear under the analysis of
either field. In the app, the results of *Gradient versus random search* and
*Analytic surrogate* (Sections 3.3 and 3.7, Tables S3 and S12) are the files imported
from the bundled run in step 5.

**Other settings and your own schedules.** The same view runs any analysis with other
settings, at other fields, or on your own schedules, for example a fingerprint from the
Design view (**Inputs** › **Add the designed fingerprint**). The tooltip of each
analysis and setting says what it does.

### The same steps on the command line

On a Mac or Linux, in Terminal (`APP` is the path of the program):

```bash
APP=~/Applications/pyDMRSF               # where the file pyDMRSF is
R=~/pyDMRSF_results/paper
# step 5: the results of the searches, and of the two analyses that need PyTorch
"$APP" --pipeline import run_2026-10-20 --searches --results "$R"
# steps 6 to 8: every analysis at 3 and 7 T, two at a time; imported results are kept
"$APP" --pipeline run all --fields 3 7 --jobs 2 --resume --results "$R"
# step 10
"$APP" --pipeline compare --results "$R" --with run_2026-10-20
```

On Windows, in PowerShell:

```powershell
$APP = "$HOME\Downloads\pyDMRSF.exe"       # where the file pyDMRSF.exe is
$R = "$HOME\pyDMRSF_results\paper"
& $APP --pipeline import run_2026-10-20 --searches --results $R
& $APP --pipeline run all --fields 3 7 --jobs 2 --resume --results $R
& $APP --pipeline compare --results $R --with run_2026-10-20
```

`--jobs` sets how many analyses run at once. `compare` ends with status 1 when a file
differs.

---

## 5. The command line

| Command | What it does |
| --- | --- |
| `pyDMRSF` | opens the Workbench |
| `pyDMRSF --version` | prints the version |
| `pyDMRSF --selftest` | checks the app without opening a window (exit status 0, or 1 on a failure) |
| `pyDMRSF --screenshot FILE.png` | saves a picture of the window as it opens. With `--fingerprint`, of the MRF fingerprint tab after a design (about a minute); with `--pipeline-view`, of the Pipeline view |
| `pyDMRSF --classic` | opens the classic tools, if this copy includes them |
| `pyDMRSF --pipeline …` | runs and compares analyses without the window (below) |

`pyDMRSF` stands for the path of the program, as in section 2.

```bash
pyDMRSF --pipeline list          # the analyses, what they read and write, and the bundled results folders
pyDMRSF --pipeline settings      # the switches of the model and the fit, with their defaults
pyDMRSF --pipeline run panel_evaluation fit_sensitivity --fields 3 7 --results DIR
pyDMRSF --pipeline run all --dry-run --results DIR                  # the plan only
pyDMRSF --pipeline run panel_evaluation --set MRSF_NUIS_RIDGE=0.1 --quick --results DIR
pyDMRSF --pipeline import run_2026-10-20                            # what can be imported
pyDMRSF --pipeline schedule add "My design" design.json --results DIR
pyDMRSF --pipeline status --results DIR
pyDMRSF --pipeline tables --results DIR
pyDMRSF --pipeline compare --results DIR --with OTHER_DIR
```

* `DIR` is a results folder. Without `--results`, the default is `pyDMRSF_results` in
  your home folder, or the folder in the environment variable `PYDMRSF_RESULTS`.
* `run_2026-10-20` names the results folder that comes inside the app. It can be used
  wherever a folder is expected.
* `--jobs N` runs N analyses at once.

---

## 6. Troubleshooting

* **macOS says that pyDMRSF "cannot be opened", "is damaged" or "cannot be
  verified".** Allow the app to run, as *On a Mac* in section 2 explains. Do it again
  for any copy that you download again.
* **macOS: nothing happens, or the Terminal window closes at once.** Drag `pyDMRSF`
  into a Terminal window and press Return, to read the program's messages.
* **Linux: the window does not open, and the terminal says that the Qt platform plugin
  "xcb" could not be loaded.** Install the OpenGL and X11 libraries that Qt needs. On
  Debian and Ubuntu: `sudo apt-get install libgl1 libegl1 libxcb1 libxcb-cursor0`. On
  Fedora: `sudo dnf install mesa-libGL mesa-libEGL libxcb xcb-util-cursor`.
* **Linux: "version `GLIBC_2.xx' not found".** The system is older than this copy
  needs (section 1). Use a newer system, or a copy of the app built on an older one.
* **An analysis failed (✗).** The **Log** tab, and `logs/<analysis>.log` in the results
  folder, say why. **Run analyses** runs it again.
* **A run takes much longer than its plan.** The times assume a free processor core
  for each analysis. Close other heavy programs, or lower *Parallel analyses*.
* **An analysis needs PyTorch** (*Gradient versus random search*, *Analytic
  surrogate*). It cannot run in the app. Import its results (section 4, step 5).

---

## 7. More documentation

Reference guides to every control and analysis (`WORKBENCH.md`, `PIPELINE.md`,
`USER_GUIDE.md`) come with the source of pyDMRSF, in its `docs` folder. They are
written for pyDMRSF run from Python: there, `python run_gui.py` stands for the program
`pyDMRSF`, and `python -m pyDMRSF.pipeline` for `pyDMRSF --pipeline`.

pyDMRSF is based on the manuscript *"Quantum spin dynamics and multi-sequence
acquisition optimization for MRS fingerprinting in the prostate"* by Dr Nikolaos
Dikaios. The validated spin-dynamics engine is his work. pyDMRSF adds the interface,
the pipeline runner, the tests, the documentation and the packaging.
