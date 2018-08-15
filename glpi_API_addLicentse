# -*- coding: utf-8 -*-

import os
import json
import requests
import csv

URL_API = 'http://path/to/glpi/apirest.php'
USER_API_TOKEN = 'XXXX'
APP_TOKEN = 'XXXX'


class GLPIAPIClient():
    """ GLPI API Client """

    def __init__(self, url_api, app_token, user_api_token):
        """
        Create GLPI API Client
        """

        self.session_token = None
        self.url_api = url_api
        self.app_token = app_token
        self.user_api_token = user_api_token

    
    def connect(self, headers={}):
        headers = {
            'Content-Type': 'application/json',
            'Authorization': 'user_token ' + self.user_api_token,
            'App-Token': self.app_token
            }
        self.session_token = requests.get(self.url_api + '/initSession', headers=headers).json()['session_token']

    """ Request """
    def request(self, method, url, accept_json=False, headers={},
                params=None, json=None, data=None, files=None, **kwargs):
        headers = {
            'Content-Type': 'application/json',
            'Session-Token': self.session_token,
            'App-Token': self.app_token
        }
        full_url = '%s/%s' % (self.url_api, url.strip('/'))
        response = requests.request(method=method, url=full_url,
                                    headers=headers, params=params,
                                    data=data, **kwargs)
        return response


    def close(self):
        headers = {
            'Content-Type': 'application/json',
            'Session-Token': self.session_token,
            'App-Token': self.app_token
        }
        requests.get(URL_API + '/killSession', headers=headers)
        
def read_csv(file_path):
    dic = []
    """ Read a CSV file """
    with open(file_path, 'rb') as f:
        r = csv.DictReader(f, delimiter=';')
        for row in r:
            dic.append(row)
    return dic

if __name__ == '__main__':
    r = GLPIAPIClient(URL_API, APP_TOKEN, USER_API_TOKEN)
    r.connect()
    print(r.session_token)
#    print(r.request('GET', '/SoftwareLicense').json())
#    id_icom = r.request('POST', '/SoftwareLicense',
#                        data='{"input": {"name": "My single lic", "serial": "12345", "softwares_id": "2190"}}')
    for row in read_csv('utf8.csv'):
#        print(json.dumps(row, indent=4))
#       data = '{"input": {"name": %s, "serial": %s, "softwares_id": %s, "comment": %s}}' % (row['Nazvanie'], row['SN'], row['PO_ID'], json.dumps(row, indent=4))
        data = json.dumps({"input": { "name": row['Nazvanie'],
                           "serial": row['SN'],
                           "softwares_id": row['PO_ID'],
                           "comment": json.dumps(row, indent=4, ensure_ascii=False, encoding='utf8')
                         }
                })
        id_icom = r.request('POST', '/SoftwareLicense', data=data)
        print(id_icom)
        
    r.close
