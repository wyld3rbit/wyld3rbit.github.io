---
author: arka_p
published: true
layout: post
title: Sexy command objects in Grails
date: 2015-12-31 2:22:15
categories: grails command-objects groovy best-practices
---

Sometimes a bit of new information comes along that makes wrangling code a bit more fun. This is one such story.


{% highlight groovy %}
/**
 *  @param pid Medical record number. Default unspecified
 *  @param age
 *  @param race
 *  @param sex
 *  @param totalCholesterol
 *  @param hdlCholesterol
 *  @param systolicBloodPressure
 *  @param currentSmoker
 *  @param diabetesStatus
 *  @param hyperDrug Was hypertension drug administered or not? Default is false
 *  @param precision What is the number of significant digits needed in the risk score result? Default is 3
 */
class RecalculationParamsCommand {
    String pid
    Integer age
    Integer race
    Integer sex
    Integer totalCholesterol
    Integer hdlCholesterol
    Integer systolicBloodPressure
    Integer currentSmoker
    Integer diabetesStatus
    Boolean hyperDrug
    Integer precision

    static constraints = {

        pid(nullable: false, blank: false, validator: { value, obj ->
            if (!value) {
                return 'recalculationParamsCommand.invalid.pid.message'
            }
        })
        age(nullable: false, blank: false, min: 40, max: 79, validator: { value, obj ->
            if (value && !(value instanceof Integer)) {
                return 'recalculationParamsCommand.invalid.age.message'
            }
        })
        race(nullable: false, blank: false, range: 1..20, validator: { value, obj ->
            if (value && !(value instanceof Integer)) {
                return 'recalculationParamsCommand.invalid.race.message'
            }
        })
        sex(nullable: false, blank: false, validator: { value, obj ->
            if (value && !(value instanceof Integer)) {
                return 'recalculationParamsCommand.invalid.sex.message'
            }
        })
        totalCholesterol(nullable: false, blank: false, min: 130, max: 320, validator: { value, obj ->
            if (value && !(value instanceof Integer)) {
                return 'recalculationParamsCommand.invalid.totalCholesterol.message'
            }
        })
        hdlCholesterol(nullable: false, blank: false, min: 20, max: 100, validator: { value, obj ->
            if (value && !(value instanceof Integer)) {
                return 'recalculationParamsCommand.invalid.hdlCholesterol.message'
            }
        })
        systolicBloodPressure(nullable: false, blank: false, min: 90, max: 200, validator: { value, obj ->
            if (value && !(value instanceof Integer)) {
                return 'recalculationParamsCommand.invalid.systolicBloodPressure.message'
            }
        })
        currentSmoker(nullable: false, blank: false, validator: { value, obj ->
            if (value && !(value instanceof Integer)) {
                return 'recalculationParamsCommand.invalid.currentSmoker.message'
            }
        })
        diabetesStatus(nullable: false, blank: false, validator: { value, obj ->
            if (value && !(value instanceof Integer)) {
                return 'recalculationParamsCommand.invalid.diabetesStatus.message'
            }
        })

        hyperDrug(nullable: true, blank: false, validator: { value, obj ->
            if (value && !(value instanceof Boolean)) {
                return 'recalculationParamsCommand.invalid.hyperDrug.message'
            }
        })
        precision(nullable: true, blank: false, validator: { value, obj ->
            if (value && !(value instanceof Integer) || !(1 <= value)) {
                return 'recalculationParamsCommand.invalid.precision.message'
            }
        })
    }

    def beforeValidate() {
        hyperDrug = hyperDrug ?: false
        precision = precision ?: 3
    }

    @Override
    public String toString() {
        return new ToStringBuilder(this)
                .append("pid", pid)
                .append("age", age)
                .append("race", race)
                .append("sex", sex)
                .append("totalCholesterol", totalCholesterol)
                .append("hdlCholesterol", hdlCholesterol)
                .append("systolicBloodPressure", systolicBloodPressure)
                .append("currentSmoker", currentSmoker)
                .append("diabetesStatus", diabetesStatus)
                .append("hyperDrug", hyperDrug)
                .append("precision", precision)
                .toString();
    }
}

/**
 *  Expecting the following params from EHR post call:
 *  encdte = encounter date in internal epic format
 *  PATID = patient identifier in internal epic format ZiD
 *  ENCBPS = Systolic blood pressure
 *  ENCBPD = Diastolic blood pressure
 *  ENCWT = Encounter weight
 *  ENCHT = Encounter Height
 *
 *  @param pid Patient id in internal Epic format
 *  @param encDate Encounter date in internal Epic format
 *  @param sbp Systolic blood pressure
 *  @param dbp Diastolic blood pressure
 *  @param weight Patient weight
 *  @param height Patient height
 *  @param hyperDrug Was hypertension drug administered or not? Default is false
 *  @param precision What is the number of significant digits needed in the risk score result? Default is 3
 */
class InitialParamsCommand {

    String pid
    String encDate
    Integer sbp
    Integer dbp
    Double weight
    String height
    Boolean hyperDrug
    Integer precision

    static constraints = {

        pid(nullable: true, blank: false, validator: { value, obj ->
            if (value && !(value.charAt(0) == 'Z')) {
                return 'initialParamsCommand.invalid.pid.message'
            }
        })
        encDate(nullable: true, blank: false)
        sbp(nullable: true, blank: false, min: 90, max: 200, validator: { value, obj ->
            if (value && !(value instanceof Integer)) {
                return 'initialParamsCommand.invalid.sbp.message'
            }
        })
        dbp(nullable: true, blank: false, validator: { value, obj ->
            if (value && !(value instanceof Integer)) {
                return 'initialParamsCommand.invalid.dbp.message'
            }
        })
        weight(nullable: true, blank: false, min:Double.valueOf(0))
        height(nullable: true, blank: false, validator: { value, obj ->
            if (value && !(value instanceof String)) {
                return 'initialParamsCommand.invalid.height.message'
            }
        })
        hyperDrug(nullable: true, blank: false, validator: { value, obj ->
            if (value && !(value instanceof Boolean)) {
                return 'initialParamsCommand.invalid.hyperDrug.message'
            }
        })
        precision(nullable: true, blank: false, validator: { value, obj ->
            if (value && !(value instanceof Integer) || !(1 <= value)) {
                return 'initialParamsCommand.invalid.precision.message'
            }
        })
    }

    def beforeValidate() {
        hyperDrug = hyperDrug ?: false
        precision = precision ?: 3
    }

    @Override
    public String toString() {
        return new ToStringBuilder(this)
                .append("pid", pid)
                .append("encDate", encDate)
                .append("sbp", sbp)
                .append("dbp", dbp)
                .append("weight", weight)
                .append("height", height)
                .append("hyperDrug", hyperDrug)
                .append("precision", precision)
                .toString();
    }
}
{% endhighlight %}
