### 1. 뮤텍스 관련 함수 (`pthread_mutex_t`)

뮤텍스는 공유 자원에 대한 동시 접근을 제어하여 경쟁 조건(Race Condition)을 방지하는 데 사용됩니다. 조건 변수와 함께 사용될 때는 조건 변수가 대기 중인 스레드를 잠금 해제하기 위해 필요합니다.

- **`pthread_mutex_init(pthread_mutex_t *mutex, const pthread_mutexattr_t *attr)`**
    
    - **설명:** 뮤텍스 객체를 초기화합니다. `mutex` 포인터가 가리키는 `pthread_mutex_t` 타입의 변수를 사용 가능한 상태로 만듭니다. `attr`은 뮤텍스의 속성을 지정하며, 보통 `NULL`을 사용하여 기본 속성으로 초기화합니다.
        
    - **용도:** 스레드 간에 공유될 자원을 보호하기 위한 뮤텍스를 생성할 때 사용합니다.
        
    - **예시:** `pthread_mutex_init(&my_mutex, NULL);`
        
- **`pthread_mutex_destroy(pthread_mutex_t *mutex)`**
    
    - **설명:** 사용이 끝난 뮤텍스 객체를 해제합니다. 뮤텍스가 잠겨 있거나 다른 스레드에 의해 사용 중일 때는 호출하면 안 됩니다.
        
    - **용도:** 프로그램 종료 시 뮤텍스가 할당했던 리소스를 반환할 때 사용합니다.
        
    - **예시:** `pthread_mutex_destroy(&my_mutex);`
        
- **`pthread_mutex_lock(pthread_mutex_t *mutex)`**
    
    - **설명:** `mutex`가 가리키는 뮤텍스를 잠급니다. 뮤텍스가 이미 다른 스레드에 의해 잠겨 있다면, 호출하는 스레드는 뮤텍스가 풀릴 때까지 대기합니다 (블록됩니다).
        
    - **용도:** 임계 영역(Critical Section)에 진입하기 전에 호출하여 공유 자원에 대한 독점적인 접근 권한을 얻을 때 사용합니다.
        
    - **예시:** `pthread_mutex_lock(&my_mutex);`
        
- **`pthread_mutex_unlock(pthread_mutex_t *mutex)`**
    
    - **설명:** `mutex`가 가리키는 뮤텍스를 잠금 해제합니다. 이 함수를 호출하면 뮤텍스를 기다리던 다른 스레드 중 하나가 깨어나 뮤텍스를 획득할 수 있습니다.
        
    - **용도:** 임계 영역에서의 작업을 마친 후 호출하여 공유 자원에 대한 독점적인 접근 권한을 해제할 때 사용합니다.
        
    - **예시:** `pthread_mutex_unlock(&my_mutex);`
        

### 2. 조건 변수 관련 함수 (`pthread_cond_t`)

조건 변수는 특정 조건이 만족될 때까지 스레드를 대기시키거나, 조건이 만족되었음을 대기 중인 스레드에게 알리는 데 사용됩니다. **반드시 뮤텍스와 함께 사용되어야 합니다.**

- **`pthread_cond_init(pthread_cond_t *cond, const pthread_condattr_t *attr)`**
    
    - **설명:** 조건 변수 객체를 초기화합니다. `cond` 포인터가 가리키는 `pthread_cond_t` 타입의 변수를 사용 가능한 상태로 만듭니다. `attr`은 조건 변수의 속성을 지정하며, 보통 `NULL`을 사용하여 기본 속성으로 초기화합니다.
        
    - **용도:** 스레드 간의 조건부 대기 및 신호 메커니즘을 설정할 때 사용합니다.
        
    - **예시:** `pthread_cond_init(&my_cond, NULL);`
        
- **`pthread_cond_destroy(pthread_cond_t *cond)`**
    
    - **설명:** 사용이 끝난 조건 변수 객체를 해제합니다. 조건 변수에 대기 중인 스레드가 없어야 합니다.
        
    - **용도:** 프로그램 종료 시 조건 변수가 할당했던 리소스를 반환할 때 사용합니다.
        
    - **예시:** `pthread_cond_destroy(&my_cond);`
        
- **`pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex)`**
    
    - **설명:** **매우 중요한 함수입니다.** 이 함수는 다음과 같은 작업을 원자적으로(atomically) 수행합니다.
        
        1. `mutex`를 언락합니다.
            
        2. 현재 스레드를 `cond`에 대한 대기 큐에 넣고 슬립 상태로 만듭니다.
            
        3. 다른 스레드가 `pthread_cond_signal` 또는 `pthread_cond_broadcast`를 호출하여 이 스레드를 깨우면, 뮤텍스를 다시 락하고 함수에서 반환됩니다.
            
    - **용도:** 특정 조건이 만족될 때까지 스레드를 효율적으로 대기시킬 때 사용합니다. 호출하기 전에 반드시 뮤텍스를 락해야 합니다.
        
    - **예시:** `pthread_mutex_lock(&my_mutex); while (!condition_met) { pthread_cond_wait(&my_cond, &my_mutex); } /* 조건이 만족되면 작업 수행 */ pthread_mutex_unlock(&my_mutex);`
        
- **`pthread_cond_signal(pthread_cond_t *cond)`**
    
    - **설명:** `cond` 조건 변수에 대기 중인 스레드가 있다면, 그 중 **최소 하나(보통은 스케줄링 정책에 따라 결정된 하나)**의 스레드를 깨웁니다. 대기 중인 스레드가 없으면 아무 작업도 하지 않습니다.
        
    - **용도:** 특정 조건이 만족되었음을 대기 중인 스레드에게 알릴 때 사용합니다. 이 함수를 호출하는 스레드는 반드시 뮤텍스를 락한 상태일 필요는 없지만, 일반적으로는 뮤텍스 보호 하에 공유 자원의 상태를 변경한 후 호출하는 것이 좋습니다.
        
    - **예시:** `pthread_mutex_lock(&my_mutex); condition_met = true; pthread_cond_signal(&my_cond); pthread_mutex_unlock(&my_mutex);`
        
- **`pthread_cond_broadcast(pthread_cond_t *cond)`**
    
    - **설명:** `cond` 조건 변수에 대기 중인 **모든** 스레드를 깨웁니다. 대기 중인 스레드가 없으면 아무 작업도 하지 않습니다.
        
    - **용도:** 조건이 만족되어 여러 스레드가 동시에 작업을 진행할 수 있을 때 (예: 작업 큐에 여러 아이템이 생겼을 때 모든 작업자 스레드를 깨울 필요가 있는 경우) 사용합니다. `pthread_cond_signal`과 마찬가지로 호출 시 뮤텍스를 락할 필요는 없지만, 일반적으로 뮤텍스 보호 하에 호출합니다.
        
    - **예시:** `pthread_mutex_lock(&my_mutex); new_data_available = true; pthread_cond_broadcast(&my_cond); pthread_mutex_unlock(&my_mutex);`
        

### 정리: 일반적인 사용 흐름

1. **초기화:**
    
    - `pthread_mutex_init()`으로 뮤텍스 초기화
        
    - `pthread_cond_init()`으로 조건 변수 초기화
        
2. **조건부 대기 (Wait):**
    
    - `pthread_mutex_lock()`으로 뮤텍스 잠금
        
    - `while (조건이 만족되지 않았다면)`
        
        - `pthread_cond_wait(&cond_var, &mutex_var)` 호출 (뮤텍스 해제 및 대기)
            
    - 조건이 만족되어 깨어나면 (뮤텍스 다시 획득 후) 공유 자원에 대한 작업 수행
        
    - `pthread_mutex_unlock()`으로 뮤텍스 해제
        
3. **조건 신호 (Signal/Broadcast):**
    
    - `pthread_mutex_lock()`으로 뮤텍스 잠금 (선택 사항이지만 권장)
        
    - 공유 자원의 상태 변경 (조건이 만족되도록)
        
    - `pthread_cond_signal()` 또는 `pthread_cond_broadcast()` 호출하여 대기 중인 스레드 깨움
        
    - `pthread_mutex_unlock()`으로 뮤텍스 해제
        
4. **해제:**
    
    - `pthread_mutex_destroy()`으로 뮤텍스 해제
        
    - `pthread_cond_destroy()`으로 조건 변수 해제
        

이러한 함수들을 조합하여 프로듀서-컨슈머 문제와 같은 복잡한 스레드 동기화 문제를 효율적이고 안전하게 해결할 수 있습니다.