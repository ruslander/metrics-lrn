
Time elapsed, in high resolution (preferably microseconds; milliseconds is okay; one-second is mostly useless).
When I ask for this counter, it simply tells me either the time of day, or the server’s uptime, or something
like that. It can be used to determine the boundaries of an observation interval, defined by two measurements.
It needs to be consistent with the other metrics that I’ll explain next.

The number of queries (statements) that have completed.

The current number of queries being executed.

The total execution time of all queries, including the in-progress time of currently executing queries, in high
resolution. That is, if two queries executed with 1 second of response time each, the result is 2 seconds,
no matter whether the queries executed concurrently or serially. If one query started executing .5 seconds ago
and is still executing, it should contribute .5 second to the counter.

The server’s total busy time, in high resolution. This is different from the previous point in that it only shows
the portion of the observation interval during which queries were executing, regardless of whether they were
concurrent or not. If two queries with 1-second response time executed serially, the counter is 2. If they
executed concurrently, the counter is something less than 2, because the overlapping time isn’t double-counted.

    global timestamp;
    global concurrency;
    global busytime;
    global totaltime;
    global queries;

    function run_query() {
      local now = time();
      if ( concurrency ) {
        busytime += now - timestamp;
        totaltime += (now - timestamp) * concurrency;
      }
      concurrency++;
      timestamp = now;

      // Execute the query, and when it completes...

      now = time();
      busytime += now - timestamp;
      totaltime += (now - timestamp) * concurrency;
      concurrency--;
      timestamp = now;
      queries++;
    }

Notes:
    https://www.xaprb.com/blog/2011/10/06/fundamental-performance-and-scalability-instrumentation/