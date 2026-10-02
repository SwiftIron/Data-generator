# Data-generator
A repo aimed at creating synthetic data to crash test features
Previous repo deleted in favour of this one.

schema-generator

A single-file, offline generator of synthetic Swedish school timetables in the Novaschem/Skola24 Textfil GR format (.TXT) and as CSV.

Its purpose is to let you test, demo and develop systems that consume timetable exports without ever handling a real export. Real schedule files contain personal data (teacher names, signatures, e-mail addresses) and, even after anonymisation, enough context — unit names, calendars, group conventions — to identify the school. This tool produces files with the same shape and the same kinds of messiness, but every name, place, signature and date in them is generated.

What it is for
Testing importers, parsers and attendance/absence systems against realistic input
Load testing with many schools of different sizes
Demonstrations and training material where no real school may appear
Reproducing data-quality problems (encoding damage, duplicate codes, missing group links, double bookings) on purpose, at a chosen severity

It is not a scheduling tool and its output must not be imported into a production system.

How to use it
Open index.html in a browser. There is nothing to install and the page makes no network requests.
Adjust the parameters, or keep the defaults (a 4–9 school with three classes per year).
Press Generera, inspect the tabs, press Ladda ner.

Ny seed rerolls the random seed; Nytt namn draws a new fictional school and municipality. With Ny seed vid varje körning switched off, the same seed and parameters always produce the identical file, which is useful for reproducible test cases. Antal skolor downloads several schools in one go, all under the same fictional municipality.

What a generated file contains

The Novaschem layout, in the order real exports use it:

Table	Content
[Source] header	fictional school and municipality, BaseYear/OffsetYear/CreationDate consistent with the chosen school year
Room (6600)	classrooms, specialist rooms sized from demand, group rooms, library, hall, dining hall
Subject (6400)	subject codes in the chosen style, optional full names and colours, structural entries (Arbetstid, Rast, Konferens, APT, EHT …)
Teacher (6000)	teachers, mother-tongue teachers, special educators, resource staff, after-school staff, administration
Group (6200)	classes (Class=1, mentors in Teacher), half-class groups, Sv/SvA groups, slöjd and hkk/tk rotation groups, språkval groups, mother-tongue groups, with IClass set
PeriodGeneral (8100) / Period (8000)	the school-year calendar as d/m-d/m week ranges, with holidays, half weeks and study days; periods HT/VT, optionally UV/JV and P1–P4
TA (8200)	the tjänstefördelning the lessons are generated from: minutes per week per subject, group and teacher, with block names
Lesson (7100)	lessons, lunch, mentor time, breaks, planning, meetings, break duty, resource rows, special-needs rows, mother-tongue lessons, travel between units

Lessons come from the TA rows the way a scheduler would make them: minutes per week are split into lessons of sensible length, placed with a conflict-checked scheduler for classes, teachers and rooms. Resource staff get parallel rows without a group; co-teaching is written as two signatures on one row (Abc,Def); teachers who leave mid-year hand their rows to a replacement with explicit week ranges.

What is fictional, and how
People: first and last names are drawn from generic pools of common Swedish and immigrant-background names. Signatures are derived from those names in a selectable style. E-mail addresses use the fictional school domain.
Places: school, municipality and other unit names are compounds of nature words (for example Granliden, Hallonbacken). They may by chance coincide with a real place name somewhere in Sweden; none refers to a real school.
Calendar: the school year is computed from dates (ISO weeks, Easter, public holidays, regional sportlov week), not copied from any real calendar.
Everything else — rooms, groups, subjects, lessons, GUIDs — is generated from the seed.

The file contains no data taken from any real export. The generator never reads an input file.

Parameters
Skola — year range (F–3 up to 1–9), classes per year, class label style, optional group-name prefix, seed.
Läsår — start year, start/end week and weekday, sportlov region, Easter placement, number of study days, which period types to emit, and whether period-based rows are written with a period name or an explicit week list.
Personal — staffing level, teaching load, mentors per class, part-time share, staff on leave, mid-year turnover, counts of administrative, special- needs, resource and mother-tongue staff.
Skoldag & salar — day start and per-stage end times, lunch length, passing time, optional morning break, room counts and naming style.
Ämnen & grupper — Sv/SvA parallels, slöjd and hkk/tk half-class rotation, NO/SO split in 7–9, språkval, mother-tongue tuition, after-school care, number of school units.
Namnprofiler — signature style, teacher-table naming, subject-code style, group separators, språkval group naming, IClass for cross-class groups, role vocabulary, multi-teacher separator, full names, colours.
Resurs & samundervisning — how resource and special-needs rows are expressed, share of co-teaching classes and lessons.
Övriga poster — which structural entries to include.
Kvalitet & fil — data quality level, encoding, output format, TA flag style, batch size.
Data quality levels
Level	Effect
Perfekt	consistent file; the Kontroller tab reports zero conflicts
Realistisk	the quirks a well-kept real export has: a subject under two codes, a group missing its class link, a lesson with an explicit week list, "Lektion" rows and empty subjects, a substitute signature — still without double bookings
Stökig	adds double-booked rooms, inconsistent subject names, a duplicated signature, unknown signatures, stray whitespace, and sometimes å/ä/ö damage across the whole file
Kaos	adds zero-length rows, rows outside the school day, weeks outside the school year, a weekend row, and always damages the encoding
Output format notes
Default encoding is Windows-1252 with CRLF line endings, as Novaschem writes it; UTF-8 is available.
Multiple teachers on one row are comma-separated without a space.
Class (6210) in the Group table is a 1/0 flag; the parent class of a subgroup is in IClass (6206).
Period-based rows can carry the period name in Period with Week empty, or an explicit week list in Week with Period empty, or a mix; the ActualWeeks column is always the expanded list.
In the TA table CreateLesson is 1 and OriginalRecord points at the original row for rows created by a mid-year teacher change; both can be left blank instead.
Limitations
The scheduler is deliberately simple. Tight configurations (a long-day year 6 in a 4–6 school, very small staffing percentages) can leave a few lessons unplaced; the log names them and a longer day or more staff fixes it.
There is no student table; the format exports none.
Minute allocations are shaped by the national timplan but are not an exact reproduction of any school's tjänstefördelning.
Safe use
Leave Namn and Kommun empty, or enter invented ones, if you intend to share the output.
Do not add real exports to this repository, not even anonymised ones.
Generated files are for testing only.
License

MIT — see LICENSE.
