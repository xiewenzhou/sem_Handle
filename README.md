Send and recv event between processes/threads in very few lines of codes.

  * Unlimited buffer size 
  * WITHOUT any initialization or daemon process.
  * Fast and simple
  * use semaphore and share memory
      
Usage:

server.c

    #include <sem-event.h>
    
    void main() {
      
      for (;;) {
        char *buf;
        int len;
         
        // wait event from channel 123. timeout 1s.
        if (sem_event_get(123, &buf, &len, 1000) == 0) {
         // if not timeout
         // buffer is available until next sem_event_get call.
         printf("got: %s\n", buf);
        }
      }
      
    }

client.c

    #include <sem-event.h>
    
    void main() {
      // post event to channel 123.
      sem_event_post(123, "hello", 6);
    }

Usage2:

server.c

    #include <sem-event.h>
    
    void main() {
      
      for (;;) {
        char *buf;
        int len;
        
        // get event from channel 123. wait forever(timeout=0).
        if (sem_event_get(123, &buf, &len, 0) == 0) {
         char ans[128];
         sprintf(ans, "time now is %d", time(NULL));
         sem_event_ack(123, buf, ans, strlen(ans)+1);
        }
      }
      
    }
  
client.c

    #include <sem-event.h>
    
    void main() {
      char *ans;
      int ans_len;
      
      // post an empty event to channel 123 and wait for answer.
      if (sem_event_push(123, "", 0, &ans, &ans_len, 0) == 0) {
        printf("server says: %s\n", ans);
      }
    }


Build Requirements:

    gcc version > 4.6
    Linux
    
Library function:

    sem_open(): open a semaphore.
    sem_close(): close a semaphore.
    sem_unlink(): delete a semaphore.
    sem_wait(): test a firm semaphore, if greater than 0, then sub 1, if equal 0,then sleep.
    sem_post(): add 1 to semaphore value.
    sem_getvalue(): get the value of semaphore.
 
   
