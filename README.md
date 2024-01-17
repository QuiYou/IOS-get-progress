

 int getkeyPro(NSString *Name) {
 
    @autoreleasepool {
        size_t length = 0;
        static const int mib[] = {CTL_KERN, KERN_PROC, KERN_PROC_ALL, 0};
        int err = sysctl((int *)mib, (sizeof(mib) / sizeof(*mib)) - 1, NULL, &length, NULL, 0);
        
        if (err == -1) {
            err = errno;
        }
        
        if (err == 0) {
            struct kinfo_proc *procBuffer = (struct kinfo_proc *)malloc(length);
            if(procBuffer == NULL) {
                return -1;
            }
            
            sysctl( (int *)mib, (sizeof(mib) / sizeof(*mib)) - 1, procBuffer, &length, NULL, 0);
            
            int count = (int)length / sizeof(struct kinfo_proc);
            for (int i = 0; i < count; ++i) {
                const char *procname = procBuffer[i].kp_proc.p_comm;
                if (strstr(procname, Name.UTF8String)) {
                    int pid = procBuffer[i].kp_proc.p_pid;
                    free(procBuffer);
                    return pid;
                }
            }
            free(procBuffer);
        }
        return -1;
    }
}
- (void)getKeyProcesses
{
    NSString *Name = @"ShadowTrackerExt";
    pid_t PID = getkeyPro(Name);
    if (PID != -1)
    {
        if (isProgress == false)
        {
            isProgress = YES;
            self.Modelhave = YES;
            [SpeedUDIDHelper yzaction];
        }
        return;
    }
    isProgress = NO;
    self.Modelhave = NO;



