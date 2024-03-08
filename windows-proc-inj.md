```
flowchart LR
    subgraph d [Technique: Process Injection]
        style d fill:#FF8888
        subgraph a [Objective: Write Malicous Code to Another Process' Memory]
            style a fill:#FFFB88
            subgraph operation1[Operation: Process Access]
                direction TB
                OpenProcess:::osApi --> NtOpenProcess:::native
            end
            operation1:::operation
        
            PID:::param-.->operation1
            AccessMask:::param-.->operation1
            operation1-.->ProcessHandle:::param
            ProcessHandle:::param -.->|Access Right Req: \nPROCESS_VM_OPERATION| operation2      
            subgraph operation2[Operation: Allocate Memory]
                direction TB
                VirtualAllocEx\nVirtualProtectEx:::osApi --> NtAllocateVirtualMemoryEx\nNtProtectVirtualMemory:::native
            end             
            operation2:::operation
            operation2-.->MemoryPointer:::param
            ProcessHandle:::param -.->|Access Right Req: \nPROCESS_VM_WRITE\nPROCESS_VM_OPERATION | operation3  
            subgraph operation3 [Operation: Write to Memory]
                direction TB
                WriteProcessMemory:::osApi-->NtProtectVirtualMemory\nNtWriteVirtualMemory\nNtFlushInstructionCache:::native
            end
            operation3:::operation
            MemoryPointer:::param-.->operation3
            CodeToWrite:::param-.->operation3 

            click OpenProcess "https://next.d3fend.mitre.org/dao/artifact/d3f:OSAPITraceProcess/"
            click NtOpenProcess "https://next.d3fend.mitre.org/dao/artifact/d3f:WindowsNtOpenProcess/"
            click VirtualAllocEx\nVirtualProtectEx "https://next.d3fend.mitre.org/dao/artifact/d3f:OSAPIAllocateMemory/"
            click NtAllocateVirtualMemoryEx\nNtProtectVirtualMemory "https://next.d3fend.mitre.org/dao/artifact/d3f:WindowsNtAllocateVirtualMemoryEx"
            click WriteProcessMemory "https://next.d3fend.mitre.org/dao/artifact/d3f:OSAPIWriteMemory"
            click NtProtectVirtualMemory\nNtWriteVirtualMemory\nNtFlushInstructionCache "https://next.d3fend.mitre.org/dao/artifact/d3f:WindowsNtProtectVirtualMemory"
        end

        subgraph b [Objective: Execute Code]
            style b fill:#FFFB88
            subgraph technique1[Sub-technique: Portable Execution Injection]
                style technique1 fill:#FFCE88
                subgraph operation4 [Operation: Create Thread]
                    direction TB
                    CreateRemoteThread\nCreateRemoteThreadEx:::osApi --> NtCreateThreadEx:::native
                end
                operation4:::operation
                ProcessHandle:::param -.->|Access Right Req: \nPROOCESS_CREATE_THREAD\nPROCESS_QUERY_INFORMATION\nPROCESS_VM_OPERATION\nPROCESS_VM_WRITE\nPROCESS_VM_READ | operation4 
            
                click CreateRemoteThread\nCreateRemoteThreadEx "https://next.d3fend.mitre.org/dao/artifact/d3f:OSAPICreateThread/"
                click NtCreateThreadEx "https://next.d3fend.mitre.org/dao/artifact/d3f:WindowsNtCreateThreadEx/"
            end
            
        
            subgraph technique2[Sub-technique: Thread Hijacking]
                style technique2 fill:#FFCE88
                subgraph operation5 [Operation: Get Thread ID]
                    direction TB
                    CreateToolhelp32Snapshot:::osApi
                end
                operation5:::operation
                PID:::param -.-> operation5
                operation5 -.-> ThreadID:::param
                subgraph operation6 [Operation: Open Thread]
                    direction TB
                    OpenThread:::osApi --> NtOpenThread:::native
                end
                operation6:::operation
                ThreadID:::param -.-> operation6
                operation6 -.-> ThreadHandle:::param
                subgraph operation7 [Operation: Suspend Thread]
                    direction TB
                    SuspendThread:::osApi --> NtSuspendThread:::native
                end
                operation7:::operation
                ThreadHandle:::param -.-> |Access Right Req: \nTHREAD_SUSPEND_RESUME |operation7
                subgraph operation8 [Operation: Get Thread Context]
                    direction TB
                    GetThreadContext:::osApi --> NtGetThreadContext:::native
                end
                operation8:::operation
                ThreadHandle:::param -.-> |Access Right Req: \nTHREAD_GET_CONTEXT |operation8
                operation8 -.-> ThreadContext:::param
                subgraph operation9 [Operation: Set Thread Context]
                    direction TB
                    SetThreadContext:::osApi --> NtSetThreadContext:::native
                end
                operation9:::operation
                ThreadHandle:::param -.-> |Access Right Req: \nTHREAD_SET_CONTEXT |operation9
                ThreadContext:::param -.-> operation9
                subgraph operation10 [Operation: Resume Thread Context]
                    direction TB
                    ResumeThread:::osApi --> NtResumeThread:::native
                end
                operation10:::operation
                ThreadHandle:::param -.-> |Access Right Req: \nTHREAD_SUSPEND_RESUME |operation10
                operation7 -.- operation8
                operation9 -.- operation10
            end
            click OpenThread "https://next.d3fend.mitre.org/dao/artifact/d3f:OSAPITraceThread/"
            click NtOpenThread "https://next.d3fend.mitre.org/dao/artifact/d3f:WindowsNtOpenThread/"
            click SuspendThread "https://next.d3fend.mitre.org/dao/artifact/d3f:OSAPISuspendThread/"
            click NtSuspendThread "https://next.d3fend.mitre.org/dao/artifact/d3f:WindowsNtSuspendThread"
            click GetThreadContext "https://next.d3fend.mitre.org/dao/artifact/d3f:OSAPIGetThreadContext"
            click NtGetThreadContext "https://next.d3fend.mitre.org/dao/artifact/d3f:WindowsNtGetThreadContext"
            click SetThreadContext "https://next.d3fend.mitre.org/dao/artifact/d3f:OSAPISetThreadContext/"
            click NtSetThreadContext "https://next.d3fend.mitre.org/dao/artifact/d3f:WindowsNtSetThreadContext"
            click ResumeThread "https://next.d3fend.mitre.org/dao/artifact/d3f:OSAPIResumeThread"
            click NtResumeThread "https://next.d3fend.mitre.org/dao/artifact/d3f:WindowsNtResumeThread"
        end 

        operation3 --> operation4
        operation3 --> operation5
    end
    
    classDef operation fill:#A1FF88;
    classDef osApi fill:#75D1E8;
    classDef native fill:#E3B6FF;
    classDef param fill:#FFB6E9,opacity:0.75,font-size:8pt;
    classDef highlevel fill:#09E72E,opacity:0.75,font-size:8pt;
    classDef access fill:#F6E8D6,opacity:0.75,font-size:8pt;

```