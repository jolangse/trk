General:
===========================

trk is a simple time tracker with support for sub-tasks/activities.
It is implemented using perl, and should work with no additional
modules using perl >= 5.8 on a Unix/Linux based system.

trk was inspired by several similar simple console based trackers, like:
  * ti (http://ti.sharats.me/)
  * timed (http://adeel.github.io/timed/),
  * timetrap (https://github.com/samg/timetrap)
  * wtime (http://wtime.sourceforge.net/)

Example session:
===========================

  # Start tracking time usage:
  trk start Big coding-project

  # Track a sub-task, e.g. separate tracking for fixes:
  trk task Bugfix BUG#3247

  # Return from sub-task, keep tracking the main project/activity:
  trk main

  # Stop tracking
  trk stop

  # Get a tracking report
  trk report verbose

Usage:
===========================

With default execution of trk, data will be stored in, and read from
the directory $HOME/.trk This path may be overridden by setting the 
environment variable TRK_DIR, e.g. "env TRK_DIR=/foo/bar trk ..."

trk start <project> (if tracking project: stop-and-start, aka switch)
trk start at <YYYY-MM-DD hh:mm> <project>
trk on <project> (alias for start)

  Starts tracking of a project. If 'at' is given, start-time
  is overridden to the given time.

  If a project was already tracking, that project will be stopped
  at the startingpoint of the new tracking, i.e. implicitly switching.

trk stop
trk stop at <YYYY-MM-DD hh:mm>
trk off (alias for stop)

  Stops tracking of project, with implicit stopping of any
  current activity.

trk list [verbose]
trk projects [verbose]

   Lists all known project names. If a project/track is currently
   active, thata project will be indicated with a chevron (>).
   Adding verbose to the command will display ID hashes along
   with the project/track names.

trk task <task>
trk task at <YYYY-MM-DD hh:mm> <task>
trk activity <task>
trk activity at <YYYY-MM-DD hh:mm> <task>

  Starts tracking of an activity within the project; think task
  or sub-projects. Activities will be tallied within the project
  like the "main project", while time counts to both activity
  and project. 

  Only one activity can be ongoing at the same time,
  so starting a new activity while one is ongoing stops the current
  and starts the new one.

trk main
trk main at <YYYY-MM-DD hh:mm>

  Stops tracking ongoing activity, and keeps tracking project.

trk report 
trk report terse <project>
trk report standard <project>
trk report verbose <project>
tkr report details <project>

   Gives a tracking report for the given project.
   Using "report" with no options provide a standard report for
   the last (or current) project tracked as a shortcut.

   Terse reports include:
     * project name
     * charged customer (not implemented)
     * total time accumulated

   Standard reports include:
     * project name
     * charged customer (not implemented)
     * session start and end datetimes and time elapsed on session
     * total time accumulated

   Verbose reports include:
     * project name
     * charged customer
     * session start and end datetimes and time elapsed on session
     * activity start and end datetimes and time elapesd on activity
     * time accumulated per named task
     * note texts w/datetime (not implemented)
     * total time accumulated

   Not implemented yet:
   Detailed reports include:
     * project name
     * charged customer (not implemented)
     * session start and end datetimes and time elapsed on session
     * activity start and end datetimes and time elapesd on activity
     * pauses with start, end, comment and elapsed time (not implemented)
     * note texts w/datetime (not implemented)
     * time accumulated per named task
     * total time accumulated

The following are not implemented, but planned (ordered by priority:

trk tasks
trk activities

   Lists all known task/activity names for currently active project/track

trk status

  Displays current project, charged customer (if any),
  start time for this session, time spent on this session,
  time spent on project total,
  current activity with start time and time spent this session.


trk note Comment will be timestamped

  Adds a comment/note to the tracking. The note will not
  affect tracked time, but will be included in detailed reports.

trk charge <customer>

  Will set a customer name as meta-information. Setting the
  charged customer will replace any previously set value.

trk pause <optional description>
trk back

  Adds an activity to the project that is not counted on
  the project. Pauses will be listed on detailed reports,
  but not on terse, standard and verbose reports.

Storage format:
===========================

The directory used for storing tracking data will be $HOME/.trk unless
overridden with the environment variable TRK_DIR. In this description,
$TRK_DIR will represent the active tracking directory.

Inside $TRK_DIR each track/project will be stored in separate 
sub-directories, with a random generated ID as directory name.
The ID of the currently active project/track is stored (single-line)
in the file $TRK_DIR/current, and the last activated track (current,
if one is tracking) is stored in $TRK_DIR/last.

Meta-information, e.g. Title of the current track is stored in the
file $TRK_DIR/<id-hash>/info as a colon-separated key-value list
(currently only title is used).

Time-tracking data is stored as $TRK_DIR/<id-hash>/tracking, as a
very simple format, with start-and-stop times line-by-line:

[YYYY-MM-DD hh:mm] to [YYYY-MM-DD hh:mm] 

If a task is currently tracking, the line will be non-ended (i.e. no newline)
with only the start-time in brackets.

Sub-tasks are stored using the same princible as above, with the only
difference that they are contained within the given track's directory.
So, the ID of the last and current tasks will be stored in
$TRK_DIR/<id-hash>/current and $TRK_DIR/<id-hash>/last, the activity
it self in $TRK_DIR/<id-hash>/<id-hash>, with meta-info and tracking in
$TRK_DIR/<id-hash>/<id-hash>/info and $TRK_DIR/<id-hash>/<id-hash>/tracking.

An example of the structure:

	$HOME/.trk
	├── 1e2ad171
	│   ├── info
	│   └── tracking
	├── 373fcd96
	│   ├── info
	│   └── tracking
	├── 3b5974b0
	│   ├── current
	│   ├── ef917c75
	│   │   ├── info
	│   │   └── tracking
	│   ├── f657d08e
	│   │   ├── info
	│   │   └── tracking
	│   ├── info
	│   ├── last
	│   └── tracking
	├── 87670109
	│   ├── info
	│   └── tracking
	├── current
	└── last

Licencing:
===========================

Copyright Jon Langseth

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
of the Software, and to permit persons to whom the Software is furnished to do
so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
