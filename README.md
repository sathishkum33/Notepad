import axios, { AxiosError, AxiosRequestConfig } from "axios";
import { RefreshResponse } from "../types/auth";

const api = axios.create({
  baseURL: "http://localhost:8000",
  withCredentials: true, // required for HttpOnly refresh cookie
});

// In-memory access token
let accessToken: string | null = null;

export const setAccessToken = (token: string | null) => {
  accessToken = token;
};

export const getAccessToken = () => accessToken;

// Attach token to request
api.interceptors.request.use((config: AxiosRequestConfig) => {
  if (accessToken && config.headers) {
    config.headers.Authorization = `Bearer ${accessToken}`;
  }
  return config;
});

// Response interceptor for auto refresh
api.interceptors.response.use(
  (response) => response,
  async (error: AxiosError) => {
    const originalRequest = error.config as AxiosRequestConfig & {
      _retry?: boolean;
    };

    if (
      error.response?.status === 401 &&
      !originalRequest._retry
    ) {
      originalRequest._retry = true;

      try {
        const res = await axios.post<RefreshResponse>(
          "http://localhost:8000/refresh",
          {},
          { withCredentials: true }
        );

        const newAccessToken = res.data.access_token;

        setAccessToken(newAccessToken);

        if (originalRequest.headers) {
          originalRequest.headers.Authorization = `Bearer ${newAccessToken}`;
        }

        return api(originalRequest);
      } catch (refreshError) {
        setAccessToken(null);
        window.location.href = "/login";
      }
    }

    return Promise.reject(error);
  }
);

export default api;