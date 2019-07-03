# timbre-json-appender

A structured log appender for [Timbre](https://github.com/ptaoussanis/timbre) using [jsonista](https://github.com/metosin/jsonista).

Makes extracting data from logs easier in for example [AWS CloudWatch
Logs](https://aws.amazon.com/about-aws/whats-new/2015/01/20/amazon-cloudwatch-logs-json-log-format-support/) and [GCP Stackdriver Logging](https://cloud.google.com/logging/).

## Usage

    user> (require '[timbre-json-appender.core :as tas])
    user> (tas/install) ;; Set json-appender as sole Timbre appender
    user> (require '[taoensso.timbre :as timbre])
    user> (timbre/info "Hello" :user-id 1 :profile {:role :tester})
    {"timestamp":"2019-07-03T10:00:08Z","level":"info","thread":"nRepl-session-97b9389e-a563-4f0d-8b8a-f58050297092","msg":"Hello","args":{"user-id":1,"profile":{"role":"tester"}}}


Exceptions are included in `err` key via `Throwable->map` and contain `ns`, `file` and `line` fields:

    user> (timbre/info (IllegalStateException. "Not logged in") "Hello" :user-id 1 :profile {:role :tester})
    (timbre/info (IllegalStateException. "Not logged in") "Hello" :user-id 1 :profile {:role :tester})
    {"args":{"user-id":1,"profile":{"role":"tester"}},"ns":"user","file":"*cider-repl home/timbre-json-appender:localhost:49943(clj)*","line":523,"err":{"via":[{"type":"java.lang.IllegalStateException","message":"Not logged in","at":["user$eval11384$fn__11385","invoke","NO_SOURCE_FILE",523]}],"trace":[["user$eval11384$fn__11385","invoke","NO_SOURCE_FILE",523],["clojure.lang.Delay","deref","Delay.java",42],["clojure.core$deref","invokeStatic","core.clj",2320],["clojure.core$deref","invoke","core.clj",2306]

Data that isn't serializable is elided, to not prevent logging:

    user> (timbre/info "Hello" :o (Object.))
    {"timestamp":"2019-07-03T10:14:47Z","level":"info","thread":"nRepl-session-97b9389e-a563-4f0d-8b8a-f58050297092","msg":"Hello","args":{"o":{}}}
