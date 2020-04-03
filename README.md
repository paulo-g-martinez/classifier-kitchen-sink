# classifier-kitchen-sink
Attempt efficient classifier comparison and promotion

## This is an experiment in "elegant brute force."
- Given a "large" but "tidy" data set with a "wide" set of potentially sparse and or redundant numeric, categorical, and datetime features and multiple "imbalaced" targets (not a multi class column, but two or more binary class target columns)
  - Attempt to find an efficient way of comparing the success of various classification models 
  -   each with various model configurations

P1. One of the premises of this experiment is that it would take "too much" time to assess the viability, let alone solve the problem using a "traditional" domain-knowledge based approach.
P2. Another premise is that compute and memory resources are so limited that a brute force iteration through models would also take "too long."

## Workflow_1.0
"Failing Fast." We begin with the most difficult but easiest to compute configurations in hopes of fiding success before having to "bloat" all the way up to full brute-force iteration

For each binary-classification target
- find "natural" class_weights
- for sample_size in [small, medium, ... large, full]:
  - for weight_balance in [natural_weights, slight_upsample, ..., aggressive_upsample]:
    - for model in [rf, cnb, boost, etc]:
      - pre-process features
        - impute nulls
        - perhaps scale
        - fit, train, test model
        - score model
        - score feature importance
        - while model scores unsatisfactorily and features refinement is possible:
          - refine feature selection
          - refit, retrain, retest, rescore
          - (if model fails to score satisfactorily move on to next model)
        - **if model scores satisfactorily on sample**
          - save model, its score and config to "training_sample_weight_success"
          - while unsampled training data is available:
            - out_of_sample test and score model on increasingly larger samples
            - **if model scores satisfactorily when "generalized" to data out of its training sample:**
              - save model, its score and config to "training_sample_weight_generalsample_success"
              - (if model failed to generalize well move on to next model)
      - (when all models have been evaluated on this class weight, upsample and repeat.)
    - (when all weight resampling's have been attempted increase the samle size and repeat.)
