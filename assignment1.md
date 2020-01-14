### Assin 1 in Mongo Aggregate Framework
###### 1. list of consultants having more than one submission
~~~JavaScript
db.submission.aggregate([
    {
        $group: {
            _id: '$consultant_id',
            noOfSubmissions: {$sum: 1}
        }
    },
    {
        $match: {
            noOfSubmissions: {$gt: 1}
        }
    },
    {
        $lookup: {
            from: 'consultant',
            localField: '_id',
            foreignField: 'id',
            as: 'consultant'
        }
    },
    {
        $unwind: "$consultant"
    }
]).pretty()
~~~
###### 2. list all the interviews of consultants.
~~~JavaScript
db.interview.aggregate([
    {
        $lookup: {
            from: 'submission',
            localField: 'sub_id',
            foreignField: 'id',
            as: 'submission'
        }
    },
    {
        $unwind: "$submission"
    },
    {
        $lookup: {
            from: 'consultant',
            localField: 'submission.consultant_id',
            foreignField: 'id',
            as: 'consultant'
        }
    },
    {
        $unwind: "$consultant"
    },
]).pretty()
~~~
###### 3. list all PO of marketers.
~~~JavaScript
db.purchase_order.aggregate([
    {
        $lookup: {
            from: 'submission',
            localField: 'sub_id',
            foreignField: 'id',
            as: 'submission'
        }
    },
    {
        $unwind: "$submission"
    },
    {
        $lookup: {
            from: 'employee',
            localField: 'submission.emp_id',
            foreignField: 'id',
            as: 'marketer'
        }
    },
    {
        $unwind: "$marketer"
    },
]).pretty()
~~~
###### 4. unique vendor company name for which client location is the same.
~~~JavaScript
db.submission.aggregate([
    {
        $lookup: {
            from: 'vendor',
            localField: 'vendor_id',
            foreignField: 'id',
            as: 'vendor'
        }
    },
    {
        $unwind: "$vendor"
    },
    {
        $match: { city: {$eq: '$vendor.city'} }
    }
]).pretty()
~~~
###### 5. count of consultants which submitted in the same city.
~~~JavaScript
db.submission.aggregate([
    {
        $lookup: {
            from: 'consultant',
            localField: 'consultant_id',
            foreignField: 'id',
            as: 'consultant'
        }
    },
    {
        $unwind: "$consultant"
    },
    {
        $group: {
            _id: {
                submission_city: '$city',
                consulatant_city: '$consultant.city'
            },
            noOfSubmissions: {$sum: 1}
        }
    }
]).pretty()
~~~
###### 6. name of consultant and client who have been submitted on the same vendor.
~~~JavaScript
db.submission.aggregate([
    {
        $lookup: {
            from: 'consultant',
            localField: 'consultant_id',
            foreignField: 'id',
            as: 'consultant'
        }
    },
    {
        $unwind: '$consultant'
    },
    {
        $lookup: {
            from: 'client',
            localField: 'client_id',
            foreignField: 'id',
            as: 'client'
        }
    },
    {
        $unwind: '$client'
    }   
]).pretty()
~~~