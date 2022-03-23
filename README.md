# get_iterable_list_from_AWS_RDS_Postgres_query


```
"""
This is code to extract an iterabel list from an AWS RDS Postgres SQL database

RDS queries return nested data-types:
a string inside a tuple inside a list

    e.g.
    result_1 <class 'list'> [('31110-word1 word2',)]

The string must be at least extracted,
perhaps also processed.

"""

import boto3
import json
import psycopg2
from psycopg2 import sql, Error
from psycopg2.extras import NamedTupleCursor
from datetime import datetime


def lambda_handler(event, context):

    ###################
    # Version Printout
    ###################
    print("Version: AWS_RDS_Postgres_get_iterable_list.ipynb /|\ 2022.03.23")


    ##################
    # read the inputs
    ##################
    # project_id
    YOUR_INTPUT = float(event["YOUR_INTPUT"])


    ##################
    # Set Variables
    ##################
    target_table = "YOUR_TABLE_NAME"


    ##################################
    # Test Connection to Database RDS
    ##################################

    try:
        connection = psycopg2.connect(
            database="YOUR",
            user="YOUR",
            password="YOUR",
            host="locomex.YOUR.us-east-1.rds.amazonaws.com",
            port='5432'
        )

        # Create a cursor to perform database operations
        cursor_3 = connection.cursor()

        # cursor = connection.cursor(cursor_factory = psycopg2.extras.RealDictCursor)
        print("Connection Ok!")

    except (Exception, Error) as error:  # Failure!!
        statusCode = 430
        output = (f"Failed -> RDS Connection Error: {str(error)} \n")
        print(error)

        return {
            'statusCode': statusCode,
            'body': output
            }


    ####################
    # get iterable list
    ####################

    iterable_list = []

    list_of_THINGS = [14, 15]

    # iterate throught supplier list to get NAICS codes
    for this_THING in list_of_THINGS:

        # enter query
        query = f"""
        SELECT field_name
        FROM field_name
        WHERE field_name like 'THING_To_MATCH' AND field_name = {this_THING}
        ;
        """

        # run query
        cursor_3.execute(query)

        # get items from query
        result_1 = cursor_3.fetchall()

        # inspectin
        print( "result_1", type(result_1), result_1 )

        # iterate through query result (nested list of tuples of strings)
        for sub_item in result_1:

            # extract string from nested tuple and list
            sub_item = sub_item[0]

            # extract integer from string
            sub_item = int(sub_item.split('-')[0])

            # inspectin
            print( "sub_item", type(sub_item), sub_item )

            iterable_list.append( sub_item )

    # terminal / inspection
    print(iterable_list)

    ##############
    # End: Output
    ##############

    # OK!
    status_code = 200
    output = str(iterable_list)


    return {
        'statusCode': status_code,
        'body': output
    }
```
