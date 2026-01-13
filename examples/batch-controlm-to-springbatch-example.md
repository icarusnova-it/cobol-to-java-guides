# Batch Control-M to Spring Batch Example

> **Icarus Nova** | Example migration from Control-M batch job to Spring Batch (conceptual, NOT production code).

## Overview

This document shows how to migrate a Control-M batch job with COBOL programs to Spring Batch with Java services. This is a **conceptual example** for educational purposes.

## Legacy Batch Job (Control-M + COBOL)

### Control-M Job Definition

```
Job: CUSTOMER-DAILY-PROCESS
Schedule: Daily at 02:00
Dependencies: None

Steps:
  Step 1: Extract Customer Data
    Program: EXTRACT01
    Input: CUSTOMER.MASTER
    Output: CUSTOMER.EXTRACT
    
  Step 2: Validate Customer Data
    Program: VALIDATE01
    Input: CUSTOMER.EXTRACT
    Output: CUSTOMER.VALID
    
  Step 3: Calculate Customer Metrics
    Program: CALCULATE01
    Input: CUSTOMER.VALID
    Output: CUSTOMER.METRICS
    
  Step 4: Update Customer Database
    Program: UPDATE01
    Input: CUSTOMER.METRICS
    Output: CUSTOMER.UPDATED
    
  Step 5: Generate Report
    Program: REPORT01
    Input: CUSTOMER.UPDATED
    Output: CUSTOMER.REPORT
```

### COBOL Program Flow

```cobol
       *> EXTRACT01 - Extract Customer Data
       IDENTIFICATION DIVISION.
       PROGRAM-ID. EXTRACT01.
       
       PROCEDURE DIVISION.
       MAIN.
           OPEN INPUT CUSTOMER-MASTER-FILE
           OPEN OUTPUT CUSTOMER-EXTRACT-FILE
           
           PERFORM UNTIL EOF
               READ CUSTOMER-MASTER-FILE
               IF NOT EOF
                   IF CUSTOMER-STATUS = 'A'
                       WRITE EXTRACT-RECORD FROM CUSTOMER-RECORD
                   END-IF
               END-IF
           END-PERFORM
           
           CLOSE CUSTOMER-MASTER-FILE
           CLOSE CUSTOMER-EXTRACT-FILE
           STOP RUN.
```

## Spring Batch Migration

### Spring Batch Job Configuration

```java
package com.company.customer.batch;

import org.springframework.batch.core.Job;
import org.springframework.batch.core.Step;
import org.springframework.batch.core.configuration.annotation.EnableBatchProcessing;
import org.springframework.batch.core.configuration.annotation.JobBuilderFactory;
import org.springframework.batch.core.configuration.annotation.StepBuilderFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableBatchProcessing
public class CustomerBatchJobConfiguration {
    
    @Autowired
    private JobBuilderFactory jobBuilderFactory;
    
    @Autowired
    private StepBuilderFactory stepBuilderFactory;
    
    @Autowired
    private CustomerExtractProcessor extractProcessor;
    
    @Autowired
    private CustomerValidateProcessor validateProcessor;
    
    @Autowired
    private CustomerCalculateProcessor calculateProcessor;
    
    @Autowired
    private CustomerUpdateProcessor updateProcessor;
    
    @Autowired
    private CustomerReportProcessor reportProcessor;
    
    /**
     * Customer Daily Process Job
     * Maps to Control-M job: CUSTOMER-DAILY-PROCESS
     */
    @Bean
    public Job customerDailyProcessJob() {
        return jobBuilderFactory.get("customerDailyProcessJob")
            .start(extractCustomerDataStep())
            .next(validateCustomerDataStep())
            .next(calculateCustomerMetricsStep())
            .next(updateCustomerDatabaseStep())
            .next(generateReportStep())
            .build();
    }
    
    /**
     * Step 1: Extract Customer Data
     * Maps to Control-M Step 1: EXTRACT01
     */
    @Bean
    public Step extractCustomerDataStep() {
        return stepBuilderFactory.get("extractCustomerDataStep")
            .<CustomerRecord, CustomerRecord>chunk(1000)
            .reader(customerExtractReader())
            .processor(extractProcessor)
            .writer(customerExtractWriter())
            .build();
    }
    
    /**
     * Step 2: Validate Customer Data
     * Maps to Control-M Step 2: VALIDATE01
     */
    @Bean
    public Step validateCustomerDataStep() {
        return stepBuilderFactory.get("validateCustomerDataStep")
            .<CustomerRecord, CustomerRecord>chunk(1000)
            .reader(customerValidateReader())
            .processor(validateProcessor)
            .writer(customerValidateWriter())
            .build();
    }
    
    /**
     * Step 3: Calculate Customer Metrics
     * Maps to Control-M Step 3: CALCULATE01
     */
    @Bean
    public Step calculateCustomerMetricsStep() {
        return stepBuilderFactory.get("calculateCustomerMetricsStep")
            .<CustomerRecord, CustomerMetrics>chunk(1000)
            .reader(customerCalculateReader())
            .processor(calculateProcessor)
            .writer(customerMetricsWriter())
            .build();
    }
    
    /**
     * Step 4: Update Customer Database
     * Maps to Control-M Step 4: UPDATE01
     */
    @Bean
    public Step updateCustomerDatabaseStep() {
        return stepBuilderFactory.get("updateCustomerDatabaseStep")
            .<CustomerMetrics, Customer>chunk(1000)
            .reader(customerUpdateReader())
            .processor(updateProcessor)
            .writer(customerRepositoryWriter())
            .build();
    }
    
    /**
     * Step 5: Generate Report
     * Maps to Control-M Step 5: REPORT01
     */
    @Bean
    public Step generateReportStep() {
        return stepBuilderFactory.get("generateReportStep")
            .tasklet(customerReportTasklet())
            .build();
    }
    
    // Reader, Writer, Processor beans
    // ...
}
```

### Processor Implementation

**Extract Processor (maps to EXTRACT01):**

```java
@Component
public class CustomerExtractProcessor implements ItemProcessor<CustomerRecord, CustomerRecord> {
    
    @Override
    public CustomerRecord process(CustomerRecord item) throws Exception {
        // Filter: Only active customers (maps to IF CUSTOMER-STATUS = 'A')
        if ("A".equals(item.getStatus())) {
            return item;
        }
        return null; // Skip inactive customers
    }
}
```

### Integration with Control-M

**Transition Strategy:**

```
Control-M (Scheduler)
    ↓
n8n (Orchestration) - Optional
    ↓
Spring Batch Job
    ↓
Java Services
    ↓
Database
```

**Control-M Job Definition (Transition):**

```
Job: CUSTOMER-DAILY-PROCESS-JAVA
Schedule: Daily at 02:00
Dependencies: None

Command: 
  java -jar customer-batch.jar --spring.batch.job.name=customerDailyProcessJob
```

## Migration Comparison

### Before (Control-M + COBOL)

**Components:**
- Control-M scheduler
- JCL procedures
- COBOL programs
- VSAM/Sequential files
- DB2 database

**Flow:**
- Control-M triggers job
- JCL executes steps
- COBOL programs process
- Files updated
- Database updated

### After (Control-M + Spring Batch + Java)

**Components:**
- Control-M scheduler (during transition)
- Spring Batch framework
- Java services
- Database (same or migrated)

**Flow:**
- Control-M triggers job
- Spring Batch executes
- Java services process
- Database updated
- Reports generated

## Benefits

### Spring Batch Benefits

- **Declarative Configuration**: Job defined in code
- **Chunk Processing**: Efficient processing
- **Retry and Error Handling**: Built-in retry
- **Transaction Management**: Automatic transactions
- **Monitoring**: Built-in monitoring
- **Restartability**: Can restart from checkpoint

## Related Documents

- [Batch Flow Diagram](../diagrams/batch-flow.md)
- [Target Architecture Java](../docs/05-target-architecture-java.md)
- [Migration Strategies](../docs/06-migration-strategies.md)

---

**Note:** This is a conceptual example for educational purposes. Production implementations should include proper error handling, security, compliance, and performance optimizations specific to your environment.

**Last Updated:** 2024  
**Maintained by:** Icarus Nova Architecture Team
