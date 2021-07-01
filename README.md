# axios-refresh-token-
adding refresh token to axios interceptor


```js
/** Refresh Token implementation */


axios.interceptors.response.use((response) => {

    /**

     * Check if there is 401 status code in the response

     */

    if (response.data.status_code === 401) return refreshTokenFunction(response);

    return response;

}, /** You may add here callback to catch 401 error */);


const refreshTokenFunction = (error) => {

    const originalRequest = error.config;


    // if the refresh token is expired, go to login page

    if (error.data.status_code === 401 && originalRequest.url === `${URL_API}refresh_token`) {

        window.location.href = '/';

        return Promise.reject(error);

    }


    if (error.data.status_code === 401 && !originalRequest._retry) {

        originalRequest._retry = true;

        const refreshToken = getLocalStorage(REFRESH_TOKEN);


        return axios

            .post(`${URL_API}refresh_token`, null, {

                headers: { Authorization: `JWT ${refreshToken}` },

            })

            .then((res) => {

                if (res.status === 200) {

                    setLocalStorage(res.data.access_token, GET_TOKEN);

                    originalRequest.headers['Authorization'] = 'JWT ' + res.data.access_token;

                    return axios(originalRequest);

                }

            });

    }

    return Promise.reject(error);

};

```
