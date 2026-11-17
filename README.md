# TELE9782 Lab 6 — Hanqing Zhao

- [Read the lab report (PDF)](TELE9782_Lab6_HanqingZhao.pdf)
- [Download the editable report (Word)](TELE9782_Lab6_HanqingZhao.docx)
- [Download the Lab 6 project files and simulation results](https://github.com/1789652968/TELE9782-Lab6/releases/tag/lab6)

This package contains genuine CST Studio Suite 2024 time-domain simulations, exported results, analysis, and the completed original laboratory template. The report is in English. Paths below are relative to the root of the Lab 6 ZIP.

## Deliverables

- `Final_Report/TELE9782_Lab6_HanqingZhao.docx` and the matching PDF.
- `CST_Projects/Lab6_Baseline_WR6_5.cst` and `Lab6_Optimized_Sidelobe.cst`, with their same-named companion directories. Keep each project and its companion directory together; the latter stores simulation results.
- `Figures/`: required high-resolution plots and images. `Figures/CST_Screenshots/`: actual application-window screenshots for Steps 1–5 and the implemented optimization. These are also placed in the relevant report sections.
- `Simulation_Data/`: complex S parameters, calculated efficiencies, raw CST farfield exports, numerical summaries and comparison tables. `CST_Derived_Curves/` contains the two-column data imported into the baseline project for display; these are derived from actual simulation exports, not additional solver results.
- `Scripts/CST_Automation/`: verified VBA history commands and the safe rebuilding entry point. `Scripts/Result_Analysis/`: extraction, numerical analysis and report preparation. `Inspection/`: geometry checks, solver logs, audit records and development diagnostics.

## Final geometry and solver

Internal WR6.5 dimensions: a = 1.651 mm along x, b = 0.8255 mm along y; propagation is along +z over 0–20 mm. PEC walls are formed outward from this internal volume, with t = 0.10 mm. The baseline longitudinal slot lies in the +x shorter wall, is centred on y = 0, and has width 0.10 mm and length 20 mm. Thickness and width are engineering assumptions, not prescribed lab dimensions. The checked PEC volume is 10.506 mm³.

The optimized slot retains the 20 mm length and uses w(z) = 0.04 + 0.10 sin²(πz/20) mm, sampled every 1 mm with continuous straight edges between samples. Its PEC volume is 10.526 mm³. No shorter-slot case is misrepresented as the baseline.

Both projects use 110–170 GHz, vacuum background, open transverse radiation boundaries with added space, two single-mode waveguide ports with PEC shielding frames, Port 1 excitation, modal normalization, and the time-domain solver. The shielding frames maintain the WR6.5 port cross section at the ends of the full-length slot. Baseline port cutoff is 90.751 GHz (TE10). E/H/farfield monitors exist at 110, 125, 140, 155, 170 and 137.8 GHz; the baseline also retains 138.4 GHz from the intermediate mesh study.

Final baseline: 615,480 cells, local x/y/z mesh ceilings 0.06/0.025/0.09 mm, 22 lines per wavelength, minimum-step setting 20, stop criterion −50 dB. Optimized: 985,216 cells, ceilings 0.07/0.025/0.10 mm, 18 lines per wavelength, minimum-step setting 12, minimum mesh step 0.02 mm, ratio limit disabled, stop criterion −50 dB. The education license allows at most 1,000,000 cells.

## Verified results and conventions

The laboratory efficiency is `(1-|S11|²-|S21|²)/(1-|S11|²)`, computed from complex linear S data. Its sampled baseline maximum is 96.461% at 137.800 GHz, with S11 = −8.035 dB and S21 = −15.254 dB. Sampling is 0.05 GHz. The incident-power radiated fraction is about 81.30%. CST's built-in radiation-efficiency denominator uses net accepted power across both ports and therefore gives a different number (about 99.41%).

At 137.8 GHz, the baseline global xz cut has 13.245 dBi peak directivity, θ = 44.25°, 7.406° half-power width and −5.685 dB strongest secondary lobe from 0.25° exported samples. CST polar markers round these to 13.2 dBi, 44.5° and 7.4°. The beam-aligned radiation E-plane width is about 38.4°. Global θ starts at +z; the lecture broadside angle is 90° − θ. Guide-aligned near-field E/H planes are x = 0 and y = 0 respectively; they are not both identical to the farfield principal-plane conventions.

At the same frequency and in the same full 0–360° cut, the taper gives −7.859 dB strongest secondary lobe: a 2.174 dB reduction. Its peak directivity is 15.705 dBi, direction 44.5°, width 8.350°, and accepted-input efficiency 64.300%. More power reaches Port 2. This is a measured sidelobe trade-off, not an overall efficiency improvement or a global optimum.

The sidelobe algorithm takes the global peak, bounds its lobe by the adjacent local minima on the circular angular grid, and searches all remaining local maxima. The largest secondary peak is divided by the main peak and converted with 10 log10 (linear directivity is a power quantity). It includes opposite-side radiation. Half-power crossings are interpolated. Scanning curves use absolute linear total directivity, not separately normalized traces.

## Reproduction

1. Open either final `.cst` in the installed CST 2024. Its History List contains the geometry and solver setup; inspect the saved results without rerunning. The original S parameters are under `1D Results/S-Parameters`, fields under `2D/3D Results`, and patterns under `Farfields`.
2. For a fresh rebuild, use the installed CST Python executable `D:\TELE9782\CST Studio Suite 2024\Opera\code\bin\python.exe` to run `Scripts/CST_Automation/rebuild_models.py baseline` or `optimized`. It creates a new project under `Reproduced/` and refuses to overwrite an existing one. Add `--run` to start the solver. This consolidated script uses the final settings; numbered historical macros include unsuccessful mesh trials and should not simply be run in filename order.
3. `extract_sp.py` accepts an output tag and optional project filename, exporting complex S values and the laboratory efficiency. `export_ff.py` exports CST farfield data for explicitly supplied frequencies from the active project. Check the intended active project before extraction. Analysis uses Python with NumPy and Matplotlib; report generation also requires Pillow and lxml. Python dependencies are listed in `Scripts/Result_Analysis/requirements.txt`.
4. `analyze_results.py` and `analyze_farfields.py` regenerate plots and tables from the supplied exports. `build_report.py` fills the untouched original Word template at the source path below. `export_report.ps1` uses installed Microsoft Word to export the matching PDF. Local paths are explicitly recorded in the scripts and can be adapted when transferring the package.
5. Window screenshots are actual captures of CST, not reconstructed UI. The imported efficiency and comparison plots are clearly titled as derived data. The optimized comparison screenshot displays normalized linear directivity; the high-resolution comparison figure uses normalized dB directivity.

The final native models and all analysis were executed. The consolidated fresh-rebuild entry point assembles the same previously executed history commands; a separate additional full run of that convenience entry point was not performed.

## Limits and unresolved numerical uncertainty

- Coarse baseline: 209,304 cells, −40 dB. The final peak changes by 0.330 percentage points and 0.60 GHz, but the maximum pointwise efficiency change is 10.953 percentage points. Full-band mesh independence is not established.
- The taper is close to the license limit, and its narrow ends are less well resolved. Its improvement is demonstrated on the stated meshes and frequency; no separate taper mesh-convergence or broadband optimization claim is made.
- The solver reports PEC at the axial boundaries/farfield decoupling planes and a PML centre-frequency reference higher than the 110 GHz monitor. No independent PML-spacing convergence study was completed. The required full-length slot was retained.
- CST built-in efficiency slightly exceeds unity at 110 GHz (about 100.12%), consistent with a small integration/discretization discrepancy. Laboratory-formula efficiencies remain in [0,1]. Radiated-power integration and S-parameter balance differ by about 0.6% at the peak.
- No independent constant-width or length sweep was run. Open Question 1 distinguishes general electromagnetic expectations from the implemented tapered-width comparison.
- A port-field display-cache incompatibility was found after reopening the baseline. The previous complete project was backed up under `Inspection/Before_Display_Refresh/`; a same-settings refresh run completed successfully and reproduced the reported values. CST referenced missing default display-cache filenames despite retaining valid resolution-60 files. Byte-identical copies of the valid CST-generated field and display-mesh caches were restored under the expected filenames (no numerical values or original solver files were edited). The project was reopened and the TE10 field displayed correctly. `Inspection/port_display_cache_repair.json` records the filenames and hashes; `repair_port_display.py` preserves this recovery procedure.

## Sources preserved unchanged

- `C:\Users\Admin\Desktop\TELE9782\Lab5\LAB 6 - Leakywave Antenna.docx` — authoritative assessment template. SHA-256: `140d88189f6dae1a59c959917167929584475bf6846ee5271e333c1e3a768fef`.
- `C:\Users\Admin\Desktop\TELE9782\Lab5\WR28 leaky-wave antenna 26-40 GHz.mp4` — modeling procedure only; its WR28 dimensions and frequencies are not used as the lab design.
- `C:\Users\Admin\Desktop\TELE9782\Chap 10 - TELE9782.pdf` — the locally available Chapter 10 file (without `(1)` in its filename), especially slides 9–11.
- Installed CST Studio Suite 2024 help and Python/VBA interfaces.

Original source files and unrelated projects were not overwritten. Historical failures are retained as diagnostics and are not presented as successful final simulations.
