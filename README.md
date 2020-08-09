# bilateral-filter_Java

I did a bilateral filter algorithm migration work during the first year of graduate student. I hope it can be helpful to your work.

## code：
### a Java reimplement of bilateral filter
```java
public class BilateralFilter {
    public static double[][]
    bilateralfilter(double[][] Src,int width,int height,int W,double sigma_r,double sigma_s){

        double[][] Dst = new double[width][height];
        //image=level;
        double[][] image = new double[width][height];
        for (int i=0;i<width;i++){
            for (int j=0;j<height;j++){
              image[i][j] = Src[i][j];
            }
        }
        //[x y]=meshgrid(-w:w,-w:w);
        int[][] X = new int[2*W+1][2*W+1];
        int[][] Y = new int[2*W+1][2*W+1];
        for(int i=-W;i<=W;i++){
            for(int j=-W;j<=W;j++){

                X[i+W][j+W] = j;
                Y[i+W][j+W] = i;
            }
        }
        //domain_filter=exp(-(x.^2+y.^2)/(2*sigma_s^2));
        double [][] domain_filter = new double[2*W+1][2*W+1];
        for(int i=0;i<2*W+1;i++){
            for(int j=0;j<2*W+1;j++){
                domain_filter[i][j] = Math.exp(-(X[i][j]*X[i][j]+Y[i][j]*Y[i][j])/(2*sigma_s*sigma_s));
            }
        }
        for (int i=0;i<width;i++){
            for (int j=0;j<height;j++){
                //imin=max(i-w,1);
                int imin = Math.max(i-W-1,0);
                //imax=min(i+w,r);
                int imax = Math.min(i+W-1,width-1);
                //jmin=max(j-w,1);
                int jmin = Math.max(j-W-1,0);
                //jmax=min(j+w,c);
                int jmax = Math.min(j+W-1,height-1);

                double[][] I = new double[imax-imin+1][jmax-jmin+1];
                double[][] range_filter = new double[imax-imin+1][jmax-jmin+1];
                double[][] BilateralFilter = new double[imax-imin+1][jmax-jmin+1];
                double Fnorm = 0d;
                double out = 0d;
                for (int m = 0;m<imax-imin+1;m++){
                    for (int n=0;n<jmax-jmin+1;n++){
                        // I=image(imin:imax,jmin:jmax);
                        I[m][n] = image[m+imin][n+jmin];
                        // range_filter=exp(-(I-image(i,j)).^2/(2*sigma_r^2));
                        range_filter[m][n] = Math.exp(-(I[m][n]-image[i][j])*(I[m][n]-image[i][j])/(2*sigma_r*sigma_r));
                        // BilateralFilter=range_filter.*domain_filter((imin:imax)-i+w+1,(jmin:jmax)-j+w+1);
                        BilateralFilter[m][n] = range_filter[m][n]*domain_filter[m+imin-i+W+1][n+jmin-j+W+1];
                        // Fnorm=sum(BilateralFilter(:));
                        Fnorm = BilateralFilter[m][n]+Fnorm;
                        //sum(sum(BilateralFilter.*I))
                        out = BilateralFilter[m][n]*I[m][n]+out;
                    }
                }
                //normalize the output
                Dst[i][j] = out/Fnorm;
            }
        }
        return Dst;
    }

}
  ``` 
