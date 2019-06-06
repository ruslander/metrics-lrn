Instrumentation in the world of lies



    global timestamp;

    // The *current number of queries* being executed.
    global concurrency;

    // The number of *queries* (statements) that have completed.
    global queries;

    // The portion of the observation interval during which queries were executing
    // If two queries with 1-second response time executed serially, the counter is 2
    // If they executed concurrently, the counter is something less than 2, overlapping time isn’t double-counted
    global busytime;

    // The *total execution time* of all queries, including the in-progress time of currently executing queries
    // if two queries executed with 1 second of response time each, the result is 2 seconds
    // if one query started executing .5 seconds ago and is still executing, it should contribute .5 second to the counter
    global totaltime;

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
